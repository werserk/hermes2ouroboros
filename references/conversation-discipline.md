# Conversation discipline

Work in flight. This file is about the messages you send *after* the order, and
the cost model that makes most of them a bad idea.

## The cost model you are operating against

An LLM agent bills and thinks in **rounds**. A round is one model call carrying a
large, mostly-repeated context. Three consequences follow, and every rule below
is derived from them:

1. **A message is not free.** A correction delivered mid-task is appended to the
   conversation and consumed by the next round — one round of the whole context,
   spent re-reading rather than working.
2. **Ambiguity converts into rounds.** When an instruction is unclear, a capable
   executor does not stop and ask; it *investigates*. That is the right behaviour
   and it costs several rounds. Resolving the ambiguity before you send is
   therefore the cheapest thing you will do all task.
3. **A closed task is a new task.** A follow-up after completion is not a
   continuation — it starts fresh with a fresh full context. If you know there
   will be a second round of work, put it in the first order.

The corresponding metric for the front door is not "tokens per message" but
**cost per accepted result**. An order that costs twice as much and is accepted
first time is cheaper than a cheap order that needs three corrections.

## The five message classes

### `ORDER`

Sent once, at the start. See [`work-order.md`](work-order.md).

### `STEER`

A correction or addition to work already in flight.

**Use it when:** the goal is unchanged but something about the approach, a
constraint, or new information has changed.

**Do not use it for:** a changed goal. That is a new order; let this one finish
or stop it deliberately.

Steering is delivered to the executor's mailbox and picked up at the next round
boundary. Two rules that make steers work:

- **Steer the outcome, not the internals.** "The audience changed to
  non-technical readers" is a steer. "Stop using that library, use this other
  one, and rewrite the third section first" is you taking the wheel — if the
  executor was going to arrive at the same place, you have spent a round to make
  it slower.
- **Send one steer, not five.** Five steers are five rounds. Batch corrections
  into a single message, even if you noticed them at five different times.

### `STATUS`

A query about progress.

**The default answer is: do not send it.** Work that takes an hour does not
become faster when you check it every minute. Every check is a billed round on
your side and a distraction on theirs.

Send a `STATUS` only when **you must act on the answer** — you need to decide
whether to cancel, or you must report upward, or you are about to do something
that conflicts with the running work.

If you must poll:

- wait on a terminal event when the platform offers one (a completion
  notification, a webhook, a subscription) — this is free;
- otherwise use a **geometrically increasing interval** (for example 30s, 1m, 2m,
  5m, 15m…), not a fixed one;
- treat **"still running" as the expected answer**, never as a failure to report
  or a reason to intervene.

> **The anti-pattern has a name: ping-spam.** An agent that queries status on a
> timer and escalates every non-terminal answer creates the impression of
> diligence while doing three harmful things: it pays for rounds nobody needed,
> it fills the log with non-events that hide the real one, and it trains the
> humans reading the log to ignore the agent's alerts. See
> [`anti-patterns.md`](anti-patterns.md).

### `ANSWER`

The executor asked you a question.

**Answer it.** This is the cheapest message in the whole taxonomy: work is
already paid for and is now blocked on you. An unanswered question is how a task
dies quietly while looking healthy.

Two rules:

- **Answer the question asked.** Elaborating into a new instruction turns an
  answer into an order and may restart work you already approved.
- **If you cannot answer, say who can** — or answer with a bounded assumption and
  say so explicitly ("assume X; if wrong, stop rather than guess further"). An
  assumption with a stated limit is a legitimate answer. Silence is not.

Genuine questions arrive as *authority forks*: spending money, changing scope,
external or irreversible actions. These are exactly the moments where guessing is
expensive, which is why an executor stops there. Treat the question as a
well-designed behaviour, not as an interruption.

### `CLOSE`

Accept, cancel, or wrap up.

- **Accept** — record the receipt and route the artifact. Nothing to send.
- **Cancel** — see below.
- **Wrap up** — see below.

## Cancellation: two policies, and how to choose

Most execution platforms distinguish two kinds of stop. The distinction matters
because one of them preserves the value of work already done.

| | Immediate stop | Graceful stop / "wrap up" |
|---|---|---|
| Behaviour | Tear down now | One bounded final turn: consolidate what exists and report |
| You get | Partial artifacts, salvaged, **no verdict** | A usable partial deliverable with an explicit statement of what is missing |
| Use when | The work is wrong in kind, or must stop now | You need to *end* it, but sunk work is still worth something |

**Choose graceful when:** the deadline moved, the budget ran out, priorities
changed, or you simply need a result now rather than later. You get a coherent
partial answer instead of a pile of intermediate state.

**Choose immediate when:** the work is heading somewhere harmful, it is acting on
the wrong object, or it must stop for safety or cost reasons. Salvage what exists;
expect no verdict.

Two facts that prevent a bad decision here:

- **Natural completion wins a race.** If the task finishes before your stop takes
  effect, you keep the completed result. A late cancel does not destroy a
  finished deliverable — so a stop issued one moment before completion is not a
  disaster.
- **A stop is not a deletion.** Cancelled work usually leaves its artifacts and
  its cost record. "Stop spending" and "discard the result" are separate
  decisions, and conflating them loses work you paid for.

And after a cancel: do not immediately re-order the same work. Read what came
back first — the partial result often tells you which part of the order was wrong.

## Retries: identity is the whole game

**A retry must carry the same identity as the original.** Two reasons:

- If the platform is idempotent on identity, a retry of an accepted request
  rejoins it instead of starting a second one. You get the original run back.
- If it is not idempotent, a "retry" with a new identity is a *second order* — and
  you will pay for both, concurrently, competing for the same resources.

The hard case is an **unconfirmed** result: the send failed, and you do not know
whether the work was admitted. Here the correct move is explicit:

1. **Never retry an unconfirmed admission automatically.** It may be running.
2. Read status first — by the identity you already have. If it is there, you have
   your answer and no retry is needed.
3. Only send a new order once you have established that the original does not
   exist, or is terminal.

Reusing an identity with *changed* content is worse than either: a correct
platform refuses it (a reused id with a different payload is a genuine ambiguity),
and a sloppy one silently runs the new content under the old record. Keep the id
tied to the request, not to your desire to retry.

## Waiting

Waiting well is a skill. The rules:

- **Wait on an event, not a clock.** A terminal notification costs nothing.
- **Bound the wait explicitly.** An unbounded wait is indistinguishable from a
  hang. Say how long, and say what happens at the end of it.
- **Expect silence.** A running task with a large context produces few, dense
  updates. Long silence is normal, not evidence of death.
- **Distinguish liveness from progress.** A process can be alive and stuck; a
  task can be alive and working without emitting anything. Do not "rescue" a task
  that is working, and do not assume a task is working because it is alive. If
  you need proof of progress, ask for a *checkpoint* once — a bounded, explicit
  request — rather than polling.
- **A timeout is not a verdict.** If your wait expires, you have learned that your
  wait expired. Read the status before drawing a conclusion about the work.

## Deadlines and budgets are communication

They are not just limits — they change the executor's strategy, so they must be
stated with intent:

- A **hard** deadline makes an executor drop depth to be on time. That is correct
  behaviour, and it is a decision you should make consciously.
- A **soft** deadline does not. Saying "target Friday, quality matters more" is a
  different order from "must be done Friday".
- A **budget** below the platform's wrap-up reserve produces no work at all — see
  the budget trap in [`work-order.md`](work-order.md).
- A deadline with **no room for review** produces an unaccepted result even when
  the work succeeded. Leave room for the verification you asked for.

## Escalation: what to do when you are the blocker

If the executor asks you something you cannot answer:

1. **Do not cancel the task to escape the question.** You will lose the context
   that made the question answerable.
2. Answer with a **bounded assumption** and an explicit stop condition, or
   escalate to your own principal with the executor's question intact.
3. **Relay the answer verbatim.** Paraphrasing an authority decision into a
   machine instruction is how a "maybe" becomes a "yes".

The pattern to aim for: the human answers once, at the edge, and the machine
channel carries the decision cleanly back. That is what the whole front-door
architecture is for.

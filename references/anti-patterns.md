# Anti-patterns

Named failure modes, with what each one costs and the fix. These are the patterns
that make two capable agents more expensive than one mediocre agent.

---

## 1. Ping-spam

**Symptom.** A timer in the front door queries the running task every N seconds or
minutes and reports each non-terminal answer.

**What it costs.**

- A billed round per check, on a task that is not made faster by being watched.
- A log full of non-events. The real signal — the one message that says something
  changed — is now one line among two hundred identical ones.
- **Alarm fatigue in the humans reading it.** This is the expensive part. An agent
  that escalates everything has trained its readers to ignore it, so the one
  genuine alert is missed. The damage from a false alarm is not the alarm; it is
  the true one it hides.

**The fix.** Wait on a terminal event. If the platform has no event, use a
geometric interval, and treat "still running" as the expected answer — never as
something to report, escalate, or intervene over.

---

## 2. Drip-feed

**Symptom.** Five messages refining one intent: the order, then "actually, also…",
then "one more thing…", then "and I meant the other file".

**What it costs.** Every message is a round. Five messages means the executor
re-reads the same large context five times, and — worse — may have already
committed to an approach the fifth message invalidates, so you also pay for the
rework.

**The fix.** One complete order. If you notice yourself wanting to add something,
that is a signal the order was incomplete, not that the channel is working well.
Batch corrections into a single steer.

---

## 3. Script-shaped order

**Symptom.** The order is a sequence of steps: "first do X, then refactor Y, then
write Z".

**What it costs.** You have installed a ceiling on your own result. A capable
executor that can see a better path is now contractually obliged to walk the worse
one — and it will follow your plan even where your plan is wrong, because you
described it as an instruction rather than as context.

**The fix.** State the outcome and the constraints. Give the plan only where
order genuinely matters: safety steps, irreversible actions, external
side-effects, or a sequence that must be exactly reproducible. ("Change the
config *after* the backup" is a real constraint. "Open the file, then read line
40" is choreography.)

**The test:** if your step list were replaced by a better one that reached the
same outcome, would the order still be satisfied? If yes, the steps were
choreography. If no, keep them — and say *why* they must be in that order, so the
executor can preserve the reason rather than the words.

---

## 4. Pre-answering the deliverable list

**Symptom.** "Produce a report with sections A, B, C, D and E."

**What it costs.** You asked for what you already thought of. If the point was to
get the executor's judgement, you have just replaced its judgement with yours and
paid for the privilege.

**The fix.** Ask for the *question* and the *constraints*, then ask it to propose
the deliverable set and justify it — including what it considers useless. Reserve
an explicit list for cases where the shape is genuinely fixed by an external
consumer (a form, a schema, a template someone else will read).

Better: "Here is the decision I have to make. Propose what you would produce, then
produce it." The proposed set is often the most valuable part of the result.

---

## 5. Treating a typed refusal as an outage

**Symptom.** The platform answers with a specific, reasoned refusal (bad request,
unavailable route, quota exhausted, policy denied) and the front door retries it,
reports it as a failure, or escalates it as an incident.

**What it costs.** A refusal is a **deterministic verdict about this request**. It
will say the same thing next time. Retrying buys the same answer twice; reporting
it as an outage hides the actionable reason behind an alarm.

**The fix.** Read the reason. It distinguishes cases you must handle differently:

- *retryable* — a transient condition; wait and retry
- *needs a different request* — the request itself is the problem; change it
- *needs a decision* — a human or an authority must act
- *permanent* — do not retry; re-plan

Never collapse these into one "it failed" branch. The vocabulary exists precisely
so that you do not have to guess.

---

## 6. Retrying an unconfirmed admission

**Symptom.** The send failed and you do not know whether the work was accepted;
you resend.

**What it costs.** Two concurrent runs of the same work, competing for the same
resources, both billed, producing two divergent results and possibly two writes
to the same target. This is the most destructive pattern in the whole list.

**The fix.** Never retry an unconfirmed admission automatically. Read status by
the identity you already hold. Re-send only once the original is proven absent or
terminal. If a retry is necessary, it must carry the *same identity* so an
idempotent executor rejoins the original instead of starting a second one.

---

## 7. Two sources of truth

**Symptom.** The front door copies the result into its own store, or both sides
maintain a status record for the same work.

**What it costs.** Two copies drift. Whichever is read next is a coin flip, and
the wrong one looks exactly like the right one.

**The fix.** One authority per fact, and it is named in the order. Route
artifacts by reference; never re-type them. If the front door must hold a copy
for delivery, hold the *reference* plus the digest, and say which one wins when
they disagree.

---

## 8. Making the executor a chat partner

**Symptom.** Conversational requests with no completion state: "hey, could you
have a look at the deploy thing when you get a chance?"

**What it costs.** Nothing about the request is checkable, so nothing about the
result is either. You have bought an unpredictable amount of work with an
unbounded definition of done.

**The fix.** If it deserves a task, order it as a task. If it does not deserve an
order, it probably does not deserve the round-trip either — answer it yourself.

---

## 9. A budget below the wrap-up reserve

**Symptom.** A per-task cost cap chosen "to be safe" — small enough to feel
prudent.

**What it costs.** A cap at or below the platform's wrap-up reserve resolves to
*immediate finalization*: the task ends having done no work, and honestly reports
that. A cap just above it yields a ceiling of pennies and dies after its first
spend. You paid for an admission and a wrap-up and got neither work nor a
diagnostic you could act on.

**The fix.** Learn the reserve (it is an absolute amount reserved for the wrap-up
call, not a percentage) and set the cap above it. Then verify what the cap
actually bought by reading the reported ceiling, not by trusting the number you
typed.

---

## 10. Unbounded scope

**Symptom.** "Improve it", "make it better", "clean this up".

**What it costs.** Every scope is a valid answer to the order, including absurd
ones. The executor must choose, and the choice may be nothing like your intent.
Worst case: a large, correct, unrequested refactor that you now have to review.

**The fix.** Bound it with a *decision*: "improve it enough that a new developer
can add a field without reading the whole module — that is the bar." A stated bar
converts an aesthetic into a check.

---

## 11. Halting the tree on a local failure

**Symptom.** One tool, dependency, or sub-step fails, and the front door stops
everything — or marks the whole task failed.

**What it costs.** A local, recoverable refusal is promoted to a global outcome.
The independent parts of the work that were about to succeed are thrown away with
it. This is usually a property of the front door's own policy ("any error stops
the task"), and it is a design defect there.

**The fix.** Block only what depends on the failed step; keep the goal; continue
the independent branches; report the failure as a fact with its scope. A rule
that says "a broken tool stops the task" cannot distinguish
*this step is impossible* from *the entire objective is impossible*, and it will
always guess wrong in the expensive direction.

---

## 12. Status theatre

**Symptom.** The front door narrates its own watching: "still running", "checking
again", "no change yet".

**What it costs.** It consumes the reader's attention without transferring
information, and it buries the one message that matters. Reported diligence is not
diligence.

**The fix.** Report events, not the absence of events: started, blocked, needs a
decision, finished. Silence while work progresses is correct behaviour.

---

## 13. Silent authority assumption

**Symptom.** The order does not say who may do the irreversible thing, so the
executor decides, or stops.

**What it costs.** Either an action nobody approved — publishing, spending,
contacting a third party — or a stalled task waiting for a permission you would
have granted immediately.

**The fix.** Name the approvals. If you are unsure who approves publishing, the
answer is you do. "Prepare it locally; do not publish" is a complete and useful
instruction.

---

## Anti-patterns you should NOT try to fix by re-ordering

Some failures are not communication failures, and no order fixes them:

- **A vague executor.** If it returns prose where you asked for a check, that is a
  capability gap, not an ambiguity in your order. Trying to out-specify it wastes
  rounds; pick a different executor or downgrade the ask.
- **A slow executor.** Ordering it to be faster does not work. Buy less work, or
  accept the latency.
- **A wrong model of the world.** If it "knows" something false, more instruction
  does not help — give it the source that contradicts the belief, as an
  `[AUTHORITATIVE]` input.

Recognizing which class you are in is most of the skill. Ambiguity is answered
with a better order; capability is answered with a different executor;
infrastructure is answered with a retry.

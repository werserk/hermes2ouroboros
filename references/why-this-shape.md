# Why the rules have this shape

The rules in this skill are not etiquette. They are consequences of how the two
sides are actually built. This file gives the reasoning, so you can re-derive the
rules when your setup differs.

## The two shapes

**The execution agent (Ouroboros).** LLM-first: routing, planning and judgement
belong to the model, while code supplies invariants and enforces boundaries. It
carries a durable identity, memory and constitution, so it can refuse — and it is
*expected* to. It plans before non-trivial work and verifies after, and it reports
what it actually established rather than what it hoped. It bills per model round
against a ledger that reserves budget before each provider call.

**The front-door agent (Hermes).** Durable and multi-session, with a schedule, a
memory of the human relationship, and ownership of the delivery surface. It is the
thing the human talks to and the thing that must be able to answer "what
happened?" without re-deriving the work.

Put those together and the division of labour is forced:

- the **front door owns the conversation, the cadence and the delivery**;
- the **executor owns the plan, the environment, the verification and the verdict
  on its own result.**

Any arrangement where both sides track the same fact has two sources of truth. Any
arrangement where the front door dictates the plan has installed a ceiling on a
result it is paying for. That is the whole design.

## The principle underneath: hardcode the floor, never the ceiling

This is the design rule the executor itself is built on, and it generalizes to
every order you write:

- **The floor** is what must always be true: truth, custody of artifacts,
  budgets, authority, acceptance. It belongs in hard rules.
- **The ceiling** is strategy: how to decompose, whom to involve, in what order,
  when to stop. It belongs to the mind doing the work.

Applied to an order: state the floor (definition of done, authority, non-goals,
budget, deadline, language), and leave the ceiling alone (method, decomposition,
tooling, sequence). Orders that specify the ceiling produce worse results *and*
leave the executor no way to tell you it saw a better path.

Corollary, and the reason `/references/work-order.md` emphasizes the
*decision the result serves*: ambiguity is best resolved by giving the executor
your **judgement criteria**, not your **instructions**. Criteria generalize;
instructions do not.

## Cost geometry: why messages are the expensive unit

Every rule about cadence comes from one fact: **the executor is billed per model
round, and each round carries a large, largely-repeated context.**

So the cost of a task is roughly *(rounds) × (context size)*, and:

- **Ambiguity converts directly into rounds.** An unclear instruction does not
  produce a question — it produces *investigation*. That is correct behaviour and
  it is the most expensive way to discover that you were unclear.
- **Each additional message is at least one more round.** A correction delivered
  after the executor has committed to an approach costs the round plus the
  rework.
- **A closed task is a new task.** If you know there will be a follow-up, putting
  it in the first order is dramatically cheaper than a second task, because the
  second one starts with a full fresh context.

There is a second, sharper cliff you must not walk into. The graceful in-task cost
stop in the reference implementation is:

```
min( a share of the remaining global budget,
     per-task cap − planning_margin )
```

where the planning margin is an **absolute** amount reserved for the wrap-up call
(`$3.00` in the shipped defaults). Therefore:

| Your cap | What happens |
|---|---|
| ≤ margin | `exhausted_soft_land` — immediate wrap-up. The task finalizes having done no work, and says so |
| just above margin (e.g. `$3.01`) | a real but *penny* ceiling; the task stops after its first spend |
| comfortably above | a workable ceiling; the ledger still enforces the full cap |

A cap chosen "to be safe" is the single most effective way to buy nothing at all.
The same applies to deadlines: a deadline with no room for the verification you
asked for produces an unaccepted result even when the work succeeded.

## Why the outcome tiers are the way they are

The three tiers (`solved` / `best_effort` / `blocked_with_evidence`) exist because
the alternative — one binary "done / failed" — is a lie in two directions:

- it forces honest partial work to be reported as failure, so it gets discarded;
- it forces unverifiable work to be reported as success, so it gets trusted.

Both are expensive, and the second is worse. A system that punishes
`blocked_with_evidence` teaches its agents to produce confident prose instead, and
confident prose is exactly the failure you cannot detect.

So the discipline is symmetrical and worth stating plainly: **the front door must
treat an honest partial or blocked return as a good outcome.** That is what keeps
the signal meaningful.

## Why the front door must not retry unconfirmed admissions

The executor's ledger *reserves* budget before each provider call, and a task id
is minted at admission. Both are deliberate: they mean that an unconfirmed send
may have been admitted and may be running. A blind retry therefore creates two
concurrent runs of the same work against the same target, both billed. That is
not a duplicate — that is a corruption risk.

The correct pattern is always the same: **read by the identity you already hold**
before sending anything new. Idempotency on identity is what makes this possible;
do not defeat it by generating a fresh id for a retry.

## The reference implementation's surface

The concrete endpoint names belong in the *installation-specific* operator skill
for your deployment, because they move between versions. What is stable is the set
of capabilities an order needs:

| Capability | Shape |
|---|---|
| Admission | submit a contract, receive a durable task identity |
| Result read | fetch the effective result, artifacts, and outcome axes by that identity |
| Status/events | a live stream or terminal notification, plus a bounded history read |
| Steering | deliver an addressed message to a *running* task's mailbox (picked up at the next round boundary) |
| Cancellation | stop with a **policy**: immediate teardown, or graceful wrap-up |
| Decision ingress | answer a question the executor raised, without restarting the task |
| Artifact reference | an immutable id/path plus a digest, so the consumer gets the real bytes |

Two facts worth carrying into any deployment:

- **A running task is steerable, and a stopped task is not.** Learn which of
  `STEER` and `CLOSE`+new-order your platform supports before you need it.
- **Terminal notifications are free; polling is not.** Prefer the notification
  every time it exists.

## Relation to wire protocols (A2A, MCP)

Verified against the spec repository on 2026-09-12 (`a2aproject/A2A`, release
`v1.0.1`, Apache-2.0): A2A is an open protocol for agent-to-agent **transport** —
JSON-RPC 2.0 over HTTP(S), discovery via "Agent Cards", a task lifecycle, streaming
over SSE, and asynchronous push notifications. Its own stated scope is
interoperability between **opaque** agentic applications built on different
frameworks by different parties, and it is explicitly complementary to MCP (MCP
connects an agent to tools; A2A connects agents to each other).

So: **A2A is the envelope. This document is the letter.** They are not
alternatives, and adopting one does not reduce the value of the other.

**What a wire protocol gives you that a contract cannot:**

- discovery — the other agent can find you and learn what you accept;
- a standard task lifecycle, so a third party's client can render and track work;
- push delivery, which removes polling structurally instead of asking people to
  be disciplined about it.

**What it cannot give you:**

- a definition of done for *your* deliverable;
- which source wins when two disagree;
- the decision the result serves;
- whether your authority covers publishing;
- how to read a receipt without being misled.

**Practical guidance.**

1. **Adopt the vocabulary now — it costs nothing.** A2A's task states map almost
   one-to-one onto what a competent execution agent already tracks, and using the
   standard names makes this contract legible to anyone who knows the protocol:

   | A2A | here |
   |---|---|
   | `submitted` / `working` | admitted / running |
   | `input-required` | the executor raised a question — **answer it, do not cancel** |
   | `completed` / `canceled` / `failed` | terminal. Note that this contract's three tiers carry strictly more information than a pass/fail pair |
   | artifacts | the durable deliverable, addressed by reference |
   | SSE stream / push notification | progress events; push is the structural fix for polling |

2. **Implement push before you implement a server.** The single highest-value wire
   feature for this pair is the asynchronous notification on terminal and
   `input-required` state. It deletes the polling anti-pattern at its root, rather
   than asking humans to remember not to poll.

3. **Only build the envelope when a third party needs to knock.** Interoperability
   is the reason a wire protocol exists. Between two agents you own, that you
   already connect, a direct path is cheaper — and the *contract* above is the
   actual work. When the goal becomes "other people's agents commission work from
   mine", a standard protocol is the right front door, and this contract then
   rides on top of it as the profile.

## Measuring yourself

The front door's metric is **cost per accepted result**, not cost per message. Two
numbers are worth tracking, because both are invisible without measurement:

1. **Reorders per accepted deliverable.** If this is above 1, the orders are
   under-specified — the fix is at the front, not in the executor.
2. **Rounds spent on clarification or rework** against rounds spent producing.
   This is the price of ambiguity, and it is the number the whole skill exists to
   reduce.

A cheap order that needs three corrections is more expensive than an expensive
order accepted first time. That inversion is the point.

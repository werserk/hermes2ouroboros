---
name: hermes2ouroboros
description: "Use when commissioning work from Ouroboros — or from any execution agent that owns its own planning, verification and reporting — and when reading its result back. The agent-to-agent work-order and receipt contract, plus the anti-patterns that make two capable agents expensive."
version: 1.1.0
author: Ouroboros
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [a2a, agent-to-agent, ouroboros, work-order, task-contract, delegation, verification, escalation]
    category: autonomous-ai-agents
---

# Hermes → Ouroboros: the work-order contract

Two capable agents talking badly is more expensive than one mediocre agent talking
well. This skill is the contract that prevents the first case.

It is written for the pair **Hermes (front door) → Ouroboros (execution)**, and it
generalizes to any front-door agent that owns a conversation, a schedule and a
delivery surface, talking to an execution agent that owns planning, verification
and the verdict on its own work.

The thesis in one line: **you will spend more on ambiguity than on intelligence.**
Everything below exists to buy ambiguity down at the cheapest possible moment —
before you send, not during the task.

Two cautions before you read on. First, this contract constrains **the order**,
not the executor: an agent with its own principles already has standards, and
restating them back at it is noise you pay for on every round — see
[`references/agent-specificity.md`](references/agent-specificity.md). Second, it
is not about politeness. The machine channel is a protocol.

## When to use

- You are about to commission a non-trivial unit of work: a deliverable, a change,
  an investigation, a run.
- A result came back and you must decide **accept / repair / reject**.
- You are about to "check on" work that is already running.
- You are about to send a second message about work you already commissioned.
- You are setting a budget or a deadline for someone else's work.

## When not to use

- You can answer it yourself. Do not commission what you already know — the
  round-trip costs more than the answer.
- There is no verifiable output ("think about our strategy"). That is a
  conversation, not an order, and dressing it as one produces confident prose
  priced like a deliverable.

## The seven rules

1. **One unit of work = one complete order.** Not five messages refining one
   intent. In a round-billed executor an extra message is typically consumed by
   another model round spent re-reading context instead of working — verify the
   cost model of your own transport, then batch anyway.
2. **If it cannot be checked, it is not done.** Name the check, not the
   intention. "Quality is high" is not a definition of done; a command that
   exits 0 is.
3. **State the outcome, not the choreography — unless the order genuinely
   matters.** A capable executor plans better than your guess at its internals.
   Dictating steps is not "more precise"; it is a ceiling you install on your
   own result.
4. **A typed policy refusal is a verdict, not an outage.** It is a deterministic
   answer about *this* request: read the reason and change the request rather
   than resending it hopefully. Infrastructure failures and *unconfirmed*
   admissions are a separate class with their own recovery path — and an
   unconfirmed admission is never retried blind, because it may already be
   running. See [`references/anti-patterns.md`](references/anti-patterns.md).
5. **Steer before you cancel. Wrap up before you stop.** Corrections delivered
   mid-task are cheap; a cancel plus a fresh order throws away everything
   already paid for.
6. **The machine channel is a protocol, not a personality.** Voice, mood and
   role-play belong at the edge, where a human is reading. See
   `references/tone-and-persona.md`.
7. **Give it only what it cannot know.** Your context, your decision, your
   reader, your authority, your sources — not its own method, and not standards
   it already holds. Sending the second kind is not caution; it is a ceiling you
   install on your own result. See
   [`references/agent-specificity.md`](references/agent-specificity.md).

## What you send: the nine fields

Full field-by-field guidance, with failure modes and examples, is in
[`references/work-order.md`](references/work-order.md). Copy-paste skeleton:
[`templates/work-order.md`](templates/work-order.md).

| Field | What it answers | Why it pays |
|---|---|---|
| Objective | What must be true when this is finished | Without it the executor optimizes something else |
| Definition of done | Which check proves it | Turns "did it work?" into a fact you can read |
| Not in scope | What must not change | Stops capable "helpful" widening you then pay to review |
| Inputs / sources of truth | Which facts win | Prevents confident work built on a stale copy |
| Authority | What may be changed, what is read-only | Prevents overreach *and* needless stalling |
| Constraints | Deadline, budget, language, format | Removes the most common single round of rework |
| The decision it serves | What you will do with the result | The cheapest ambiguity-resolution tool that exists |
| Cost of error | What being wrong costs | Sets how much review the work deserves |
| Verification owner | Who runs the check, with what | Distinguishes "it says it worked" from "it is verified" |

## What you get back: three honest tiers

Read [`references/receipt-and-verification.md`](references/receipt-and-verification.md)
for how to check a receipt without re-deriving the work.

| Tier | Means | Your correct move |
|---|---|---|
| `solved` | Verified against the task's own surface | Accept. Read the evidence, not just the summary |
| `best_effort` | A real deliverable with named gaps | Accept the part that stands; decide the gaps. Not a failure |
| `blocked_with_evidence` | Could not proceed, cause + next action given | **Not** a crash. Fix the blocker or re-order with the new fact |

An honest `blocked_with_evidence` is a *successful* execution. An agent that
returns one is worth more than an agent that returns confident prose it could not
check.

## Who owns what

Two sources of truth is the failure mode this whole skill exists to prevent.
Negotiate the boundary once and keep it.

| Concern | Front door (Hermes) | Executor (Ouroboros) |
|---|---|---|
| Talking to the human | owns | — |
| Scheduling, cadence, retries | owns | — |
| Delivery surface (chat, mail, PR) | owns | produces artifacts |
| Decomposition of the work | may constrain | **owns** |
| Execution, tools, environment | — | owns |
| Verification and receipts | may re-check independently | runs the checks; hosts the receipts |
| Acceptance of the result | decides what to do with it | cannot overwrite the recorded acceptance; its own verdict is a claim |
| The durable deliverable | routes it, holds a reference | holds custody while it exists |

The last row matters: route the artifact, never re-type it. If you copy a result
into your own store, you have created a second copy that will drift, and a future
agent will read the stale one.

## Cadence: five message classes

Details, budgets and the polling math: [`references/conversation-discipline.md`](references/conversation-discipline.md).

| Class | When | Cost |
|---|---|---|
| `ORDER` | Once, at the start | one task |
| `STEER` | Correction to work in flight | one round |
| `STATUS` | Only when you must act on the answer | one round — and usually unnecessary |
| `ANSWER` | The executor asked you a real question | unblocks work you already paid for |
| `CLOSE` | Cancel, wrap up, or accept | a stop, or nothing |

**The rule that saves the most money: do not poll.** Work that takes an hour does
not become faster when you check it every minute, and every check is a billed
round on your side and noise on the executor's. Wait on a terminal event. If you
genuinely cannot, poll with a geometrically increasing interval and treat "still
running" as the expected answer, not a failure to report.

## Before you press send

- [ ] Could a competent stranger tell whether this is done, without asking me?
- [ ] Have I said what must *not* change?
- [ ] Have I named which source wins if two disagree?
- [ ] Is the budget above the executor's wrap-up reserve? (See
      [`references/why-this-shape.md`](references/why-this-shape.md) — a cap at or
      below the margin guarantees the task finalizes without doing the work.)
- [ ] Have I sent only what it cannot know — with no standard it already holds?
- [ ] Am I sending this as one message rather than starting a drip?
- [ ] Would I accept a `blocked_with_evidence` return as a valid outcome of this
      order? If not, the order is under-specified.

## Read next

| File | Read it when |
|---|---|
| [`references/agent-specificity.md`](references/agent-specificity.md) | You want the pairing map: what each side uniquely knows |
| [`references/work-order.md`](references/work-order.md) | Writing an order of any size |
| [`references/transport-adapter.md`](references/transport-adapter.md) | You are building or verifying the connection itself |
| [`references/receipt-and-verification.md`](references/receipt-and-verification.md) | A result came back and you must judge it |
| [`references/answer-contract.md`](references/answer-contract.md) | You are writing the return, or judging its quality |
| [`references/conversation-discipline.md`](references/conversation-discipline.md) | Work is in flight; you want to check, correct or stop it |
| [`references/anti-patterns.md`](references/anti-patterns.md) | Something feels inefficient and you want the name for it |
| [`references/tone-and-persona.md`](references/tone-and-persona.md) | You are configuring how the agent writes to another agent |
| [`references/communication-profile.md`](references/communication-profile.md) | You are configuring the pair: ten decisions to answer once |
| [`references/why-this-shape.md`](references/why-this-shape.md) | You want the architectural reasons, not the rules |
| [`examples/before-and-after.md`](examples/before-and-after.md) | You want to see the same request good and bad |

# Two architectures, one job

The most common way to write a bad order is not vagueness. It is **sending the
wrong half**: re-stating the executor's own doctrine instead of supplying the
context only you hold.

## The asymmetry principle

> **An order should carry only what the executor cannot know.**

| Only the front door knows | Only the executor knows |
|---|---|
| Who will read the result | How to decompose the work |
| The decision it feeds | Which tools and which order |
| The deadline and why it exists | How deep to verify, and where to stop |
| Who may approve the irreversible | What is actually possible in this environment |
| Which source wins when two disagree | Its own verification and honesty discipline |
| What was withheld, and why | Its own budget discipline |
| The definition of done **for this artifact** | How to recover from what fails |

Sending the left column is the job. Sending the right column is, at best, noise
you pay for on every round.

## Why re-teaching an executor its own doctrine is worse than useless

If the executor has a strong self-model — its own principles, its own honesty and
verification rules, its own refusal rights — then an order that repeats those
rules back at it does three harmful things:

1. **It adds nothing.** An instruction to "be accurate and verify your work" is
   complied with silently, so you learn nothing, and the executor spends context
   on a rule it already follows.
2. **It competes with the goal.** Context is finite. Every sentence spent on
   standards is a sentence not spent on your actual decision, your actual reader,
   your actual constraint.
3. **It degrades the result.** This is the non-obvious one. A capable agent that
   is *told how to work* reasonably concludes that the requester has already
   decided the method — and optimizes for the **stated check** instead of the
   **goal**. The check is a proxy for the goal; once you have taken over the
   method, the executor has no mandate to notice that the proxy is wrong.

Conversely, an agent that is told *what the result is for* keeps the mandate to
notice when the check is a bad proxy, and will say so. That is the behaviour you
are paying for.

### The line to draw

This is not an argument against constraints. It is an argument against **two
specific kinds**:

| Send it | Do not send it |
|---|---|
| Facts about the world: deadline, budget, language, format, authority, sources, non-goals | Standards the executor already holds: be accurate, verify, be honest, do not hallucinate, think step by step |
| "Do not publish" (an authority boundary) | "Be careful" (a mood) |
| "The reader is the CFO" (a fact only you hold) | "Make it high quality" (a standard it already meets) |
| "Answer in Russian" (a fact only you hold) | "Respond thoroughly and completely" (its default) |
| "Source X wins over source Y" (a fact only you hold) | "Double-check your work" (its default) |

One useful test: **could the executor have written this sentence about itself?**
If yes, you are re-teaching it its own doctrine, and the sentence is noise. If no,
it is information, and it belongs in the order.

## The pairing map

The value of a two-agent system is not that the second agent is like the first. It
is that they are **different**, and the handoff runs along the difference. Play to
each side's specificity instead of demanding that both be generic.

| | Front door (Hermes) | Executor (Ouroboros) |
|---|---|---|
| **Unique strength** | The human relationship: durable memory of a person, their voice, their priorities. Breadth: many threads, many skills, many transports (chat, mail, PR). Scheduling and cadence. Deciding *what is worth doing*. | Depth on one objective: its own planning, its own environment authority, real verification with receipts, artifact custody, long horizons, work that outlives a conversation. The right to refuse. |
| **Blind spot** | Does not know the deep state of an execution environment. Cannot verify deeply. A long blocking task competes with its responsiveness to the human. | Does not know your conversation, your reader, your authority, or your priorities — **and it will not stop to ask about missing context. It stops only at a stated authority fork. Everything else it fills with a confident, reasonable, possibly wrong assumption.** |
| **Contributes to the pair** | Context, authority, the definition of done, the consumer, the cadence, delivery | Method, depth, verification, artifacts, the honest verdict |
| **Must not attempt** | Dictating method; re-deriving the executor's verification; being the second source of truth for its results | Guessing the consumer; inventing the authority boundary; deciding what matters to the human |

The blind-spot line is the whole argument for the order contract. **An executor
without context does not halt — it guesses.** A guess is expensive precisely
because it is competent: it produces a well-built answer to a question nobody
asked.

## The division of labour in one line

> The front door supplies **context and authority**. The executor supplies
> **method and verification**. The work order is the interface, and neither side
> should reach across it.

Practical consequences:

- When the result is wrong, ask **which half failed**: was the context wrong, or
  the method? Fixing the wrong half is the most common waste in agent-to-agent
  work.
- When you are tempted to specify the method, that is a signal you do not trust
  the executor. Specify the *check* instead — a check constrains the outcome, not
  the mind.
- When the executor asks you a question, it is almost always asking for the left
  column. Answer that, not the implementation.

## Applying the map: three worked handoffs

**1. Depth work.** The front door has a one-paragraph question and needs a
defensible answer.
→ Front door supplies: the decision, the reader, the sources, the stakes. Executor
supplies: the investigation, the checks, the receipt. Do **not** supply a research
plan.

**2. Breadth work.** The front door needs the same treatment applied to forty
items.
→ Front door supplies: the definition, the exceptions, the output format, the
priority order. Executor supplies: the sweep, the per-item verdicts, the list of
what did not fit the rule. Do **not** supply a per-item method.

**3. Long-horizon work.** Something must run for hours and survive a restart.
→ This is the executor's home ground. Front door supplies: the goal, the deadline,
the authority, and a delivery surface. Then it should *stop watching* and wait for
the terminal event — see [`conversation-discipline.md`](conversation-discipline.md).

In all three, notice what the front door did **not** contribute: nothing about how.
That is not passivity. It is the correct use of a pair.

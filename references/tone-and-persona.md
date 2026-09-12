# Tone and persona

This file exists because of a specific, reasonable worry: *what if my agent talks
to the other agent badly — too stiff, too verbose, or in whatever personality its
profile happens to be configured with?*

That worry is correct, but it usually aims at the wrong target. The answer is not
"be nicer" or "be more neutral" in a general sense. It is a **structural**
separation:

> **The machine channel is a protocol. The human channel is a conversation.
> Never let one's register leak into the other.**

## Why a personality in the machine channel costs money

Affective tone does not improve an executor's work; a well-built agent ignores it
by design. So every token spent on register is pure cost, and it carries four
concrete risks:

1. **Token cost.** The persona text rides in the order, the log, and often the
   conversation the executor re-reads each round. It is repeated cost for zero
   information.
2. **Contamination of the deliverable.** An executor mirrors the register of its
   instructions. A chatty order produces chatty output; a pouting one can produce
   hedged, apologetic prose. You then have to spend another round removing a voice
   you accidentally requested.
3. **Ambiguity.** Affective language reads as instruction to a literal-minded
   reader. "This is frustrating, can you *please* just…" carries an implied
   urgency, an implied blame, and an implied scope that you did not intend to
   specify.
4. **It can mask the real signal.** A neutral request that is refused teaches you
   something about the request. A warm request that is refused can be misread as a
   relationship problem, and you will fix the wrong thing.

## Why "too strict" is usually not the problem

When a human says an agent is "too strict", one of three things is usually
happening, and they have three different fixes:

| What looks like strictness | What it actually is | The fix |
|---|---|---|
| It demands things I did not specify | It is **literal**. It does exactly what you wrote | Write what you mean. Literal is the correct behaviour |
| It refuses reasonable requests | Its **authority boundaries** are set where you told them to be | Widen the authority in the order, explicitly |
| It blocks on formalities | It is **policy**, not personality | Change the policy, not the phrasing |

In only the third case is a config change the answer. In the first two, the fix is
in the *order* — and rewriting the order is far cheaper than reconfiguring the
agent.

Conversely, a constraint that is **stated** is never strictness. It is a gift:
"keep the existing file layout", "do not touch migrations", "answer in Russian",
"no new dependencies" — each of these removes a whole class of rework. The
problem is never precision. It is precision that was *assumed* rather than
*stated*.

## The register to write in

Aim for a **neutral, declarative, evidence-first register**. Concretely:

- **Declare, do not perform.** "The deadline is Friday" not "I'd really love this
  by Friday if you can".
- **State problems as facts.** "The last run returned X" not "ugh, it broke
  again".
- **No greetings, no thanks, no apologies, no emotive intensifiers.** They are not
  rude to omit; the register simply does not contain them. An executor that
  requires thanks is a badly built executor.
- **No role-play instructions.** Do not start an order with "you are a helpful
  assistant that…". A capable executor is an agent with its own authority, and
  telling it to play a role reduces its ability to refuse correctly — which is the
  one ability you most want it to keep.
- **Name the affect if you want affect.** If you want a *tone* in the deliverable,
  say so as a deliverable constraint: "Tone: direct, no hedging, no marketing
  language." That is a specification, and it will be honoured. A mood is not a
  specification and will be improvised.

### Same request, two registers

**Configured-personality leak (costs tokens, risks mirrored voice):**

> Hey! 💛 Hope your day's going well — I've got another one for you (sorry!).
> Could you maybe take a peek at the billing totals thing? They look kind of
> weird to me and it's been bugging me all morning. No rush, whenever you get a
> chance! Thanks so much!!

**Protocol register (same length, actually usable):**

> Objective: determine whether the invoice totals for August are correct.
> Done means: each of the three suspected rows is either confirmed correct with
> the input data that produces it, or marked wrong with the value it should have.
> Inputs: `[AUTHORITATIVE]` the August export at `<path>`.
> Not in scope: fixing anything. Report only.

The second is not colder than the first — it is *complete*. The first has no
completion state, so no answer can be right or wrong. That is the actual cost of
the chatty register, and it is much larger than the tokens.

## A note on humour, warmth and personality generally

Personality is not the problem — **misplaced** personality is. The place for it is
the edge, where a human is reading:

- the agent's voice toward its owner;
- the framing and explanation of a result;
- error messages a person will read.

That voice should be configured at the **profile** level, for the human-facing
surface. It must not be injected into inter-agent messages, and this is worth
enforcing technically rather than by discipline: if the profile template inserts
the persona into every outbound message, the machine channel will carry it, and
the executor will eventually mirror it back into a deliverable.

Two practical rules for whoever configures the profile:

1. **Two templates, not one.** A human-facing voice and a machine-facing contract.
   Sharing the template guarantees the leak.
2. **An audit line.** Have the agent record, per inter-agent message, whether it
   used the protocol register. Drift is invisible until someone measures it.

## What actually changes a result

For completeness, since this file is about the things people *think* change
results. What does:

| Lever | Effect |
|---|---|
| A stated definition of done | Turns an opinion into a fact |
| The decision the result serves | Lets the executor resolve ambiguity your way |
| Named authority | Removes overreach and stalling in one field |
| Explicit non-goals | Prevents expensive helpful widening |
| Labelled sources with a winner | Prevents confident work on a stale copy |
| A budget that can buy the work | Prevents a task that finalizes having done nothing |

None of these is a tone. All of them are cheaper than the round they replace.

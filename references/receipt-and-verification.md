# Receipts and verification

A receipt is what you get back. Judging it correctly is the second half of the
contract — and the half where a front-door agent most often damages its own
result, either by accepting a claim it cannot check or by rejecting an honest
partial success.

## The three honest tiers

Every completed order lands on exactly one of these. They are not a quality
scale; they are three *different* outcomes that require three *different*
responses from you.

### `solved`

Verified against the task's own surface: the named check ran, and the evidence
is attached.

**Your move:** accept — and read the evidence, not just the summary. The summary
is a projection. A one-line summary can be right about the result and silent
about a limitation that matters to your actual decision.

### `best_effort`

A real, usable deliverable with explicitly named gaps: something was not
finished, not verifiable, or not reachable inside the budget or deadline.

**Your move:** accept the part that stands, and decide the gaps yourself. This is
*not* a failure and must not be treated as one. A system that punishes
`best_effort` teaches its agents to claim `solved` instead — and then you have
lost the signal entirely, which is worse than any single bad result.

Ask specifically: *which* parts are unverified, and does my decision actually
depend on them?

### `blocked_with_evidence`

The work could not proceed. The receipt names the blocker, the evidence, and the
next action someone could take.

**Your move:** this is the highest-value return you can receive, because it
arrives *before* money is spent on a wrong path. Do not log it as a crash, and do
not retry the identical order. Either remove the blocker — with the concrete
detail the receipt just gave you — or re-order with a changed scope that avoids it.

> An agent that returns an honest `blocked_with_evidence` is worth more than an
> agent that returns confident prose it could not check. Treat these returns as
> successes and you will keep receiving them; treat them as failures and they
> will stop arriving.

## Four questions to ask of any receipt

1. **What was actually run, and by whom?** A check the executor ran itself is a
   weaker fact than a check an independent party ran. Both are useful; know which
   one you have.
2. **What does the evidence cover?** A test that exercises the parser does not
   cover the API contract. Look for the named gap; if there is none, ask.
3. **Is this the artifact or a description of it?** A quotation of an artifact is
   not the artifact.
4. **What changed since the evidence was produced?** Evidence is bound to the
   state it was taken from. If the state moved, the evidence is stale — this is
   the most common false green in agent-to-agent work.

## Verification: check it the way its consumer will

The mistake is re-running the construction steps and calling that verification.
The consumer will not repeat your steps; it will *use* the thing.

- If the deliverable is a page, render it in a browser and look at it — not "the
  file exists".
- If it is a CLI, invoke the command a user would type — not "the function
  imports".
- If it is a document, read what it claims against the sources it cites.
- If it is a change, run the check the task named, from the entry point the task
  named.

Corollary: **a passing self-test is not a passing acceptance test.** The author's
own test proves the author's model of the requirement, which is exactly the thing
that may be wrong.

## Artifacts: route, do not retype

When a deliverable exists as an artifact (a file, a patch, a bundle, a document),
carry the **reference** — the durable id or path — not a transcription.

If you copy the content into your own store, you now own two copies, and the
second one will drift. A later agent will find the stale copy and act on it. The
executor's artifact store is the authority; your job is to deliver it to the right
consumer, intact.

Two practical rules:

- **Immutable names.** A delivered artifact should be addressed by something that
  cannot later resolve to different bytes.
- **Verify the copy you ship.** If you must re-materialise the file (to attach it,
  mirror it, or post it), verify its digest against the receipt. Silent
  re-encoding is a real failure mode.

## Kill the false greens

These look like verification and are not. Each has cost someone real work:

| Looks like proof | Actually proves |
|---|---|
| "The agent said it worked" | That the agent believes it (or that its summary was optimistic) |
| "The file exists" | Existence. Not content, not validity |
| "The request returned 200" | The transport worked |
| "The tests passed" | The tests that were present passed — and says nothing if the suite was edited |
| "No errors in the log" | Nobody logged an error |
| "Exit code 0" | The last stage of a pipeline succeeded (be careful with pipes and masked exits) |
| "It is committed" | A local commit exists. Not that it was pushed, or that it is the commit you think |

The general form: **verify at the surface the consumer uses, and be suspicious of
any check that cannot fail.**

A check that cannot fail is worse than no check, because it buys false confidence
at the price of a real one. This includes your own acceptance step: if your
"verification" would pass for a wrong deliverable too, it is ceremony.

## Reading evidence discipline in a receipt

Good receipts distinguish what they know from what they assume. Look for these
labels, and be worried when they are absent on a load-bearing claim:

- **known** — observed directly, with the source named;
- **stale** — known once, but the underlying state has moved;
- **missing** — not retrieved, with the reason;
- **inferred** — derived from something else, not observed.

`not found` with the exact query is a legitimate result and must be reported as
such. It is very different from "I did not look", and from "it is not there".
If your order cannot tolerate an honest `not found`, say so up front — otherwise
you will receive an inferred answer dressed as a found one.

## When the result is degraded, unaccepted, or late

You will meet these four states. Each has exactly one correct move.

| State | Means | Your move |
|---|---|---|
| Completed, but the *objective* was not met | The work ran; the goal did not | Treat as a blocked order with a better-specified objective |
| Completed, review declined to accept | The reviewer found a real gap | Read the gap, decide, re-order with the gap named |
| Completed, partial by design (deadline or budget) | A deliberate honest truncation | Decide whether to buy the rest or ship what exists |
| Failed on infrastructure | Nothing about the work was learned | The only case where a near-identical retry is correct |

Note the asymmetry: **infrastructure failure is the only class where retrying
the same request is the right move.** For every other class, the same request
produces the same answer, and you pay for it twice.

## A five-line acceptance habit

When a result arrives, read it in this order:

1. Status and tier — what kind of outcome is this?
2. The named check and its evidence — did the thing that was supposed to be
   checked actually pass?
3. The omissions and gaps — what is *not* covered?
4. The artifact reference — can I route it without retyping it?
5. Only then the prose. Prose is the last thing to trust and the first thing
   people read.

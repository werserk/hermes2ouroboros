# Before and after

Four worked examples. Same underlying request, two versions each: what a
front-door agent usually sends, and what makes it a contract.

---

## 1. Vague ask → shaped order

**Before**

> Can you look into the billing totals for August? Something looks off. Thanks!

Why this fails: there is no completion state. Every answer satisfies it — a
one-line guess, a three-week investigation, or "I checked and they look fine".
`Something looks off` carries no testable claim, so the executor must first
*discover what you meant*, which is several rounds of your money.

**After**

> **Objective:** determine whether the August invoice totals are correct, and if
> not, name the rows that are wrong.
>
> **Done means:** each of the three rows I suspect is either (a) confirmed correct,
> with the input values that produce it, or (b) marked wrong with the value it
> should have and the input that explains the difference.
>
> **Inputs:** `[AUTHORITATIVE]` the August export at `<path>`. `[REFERENCE]` last
> quarter's export, for comparison only — the schema changed in July.
>
> **Not in scope:** fixing anything. Report only; do not edit the export.
>
> **Decision this serves:** I must answer the finance team on Thursday with a
> number I can defend, so a wrong row is much worse than an incomplete list.
>
> **Budget:** `<cap>` — comfortably above the platform's wrap-up reserve.

The "after" is barely longer. What changed is that ambiguity now has nowhere to
live: the goal is stated, the check is named, the sources are ranked, and the
executor knows that *precision matters more than completeness* — which is a
judgement call it could not have made alone.

---

## 2. Script-shaped → outcome-shaped

**Before**

> 1. Open the config parser. 2. Refactor the validation into its own function.
> 3. Add a check for empty values. 4. Update the tests.

Why this fails: you described your guess at the implementation. If the real
problem is that the parser silently accepts a malformed block two layers up, this
plan fixes the wrong place — and a capable executor that can see the real problem
is now instructed not to say so.

**After**

> **Objective:** an empty value in the config parser must be rejected with an
> actionable error instead of silently becoming `""`.
>
> **Done means:** a config containing `key =` (empty value) fails to load with an
> error naming the key and the line; the existing test suite still passes; a new
> test covers the empty-value case.
>
> **Constraint that matters:** the error must name the key and line. That is the
> part users complained about.
>
> **Not in scope:** changing the config format or the file layout.

Now the executor can find the right layer. The *reason* behind the constraint is
stated, so it will preserve the reason even if it changes the words.

**The rule:** choreography is only legitimate where order genuinely matters —
safety steps, irreversible actions, external side-effects, reproducibility. And
when it does matter, say *why*, so the reason survives.

---

## 3. Drip-feed → one order

**Before** (five messages over twenty minutes)

> 1. Can you analyze our onboarding funnel?
> 2. Also, split it by signup source.
> 3. Actually, the mobile users matter most.
> 4. Can you include the drop-off at step 3 specifically?
> 5. And keep it to one page, the exec team won't read more.

Why this fails: messages 2–5 each invalidate part of the work already started. You
pay for the analysis four times, and the fourth version is the only one you
wanted. Worse, the executor may have already published the first cut somewhere.

**After** (one message)

> **Objective:** find where the onboarding funnel loses mobile users, and why.
>
> **Done means:** an analysis of drop-off by step, split by signup source, with
> mobile called out separately; the step-3 drop-off explained with the evidence
> available (or marked `not explainable from current data`, naming what is
> missing); presented as one page.
>
> **Audience:** the exec team. One page, no methodology section, one chart.
>
> **Decision this serves:** I must pick one fix to ship this quarter, so a
> confident answer about the biggest single loss matters more than a complete map
> of every step.

Everything the four follow-ups added is now in the first message, *before* the
work started, when it costs nothing.

**Rule of thumb:** if you find yourself wanting to add something, that is evidence
your order was incomplete — batch it and send one steer, not four.

---

## 4. Receiving `blocked_with_evidence`

**The receipt**

> `blocked_with_evidence`
> The staging database is not reachable from this environment: the connection
> attempt from `<host>` times out on port 5432 (attempted 3 times over 90s; see
> the log excerpt below). I can see the schema through the read replica instead,
> but it lags by up to 4 hours, so any row-level check would be unreliable.
> **Next action:** either allow this host in the staging security group, or tell
> me to proceed against the replica and accept 4-hour staleness for anything
> row-level.

**Wrong response**

> OK, retry it. Maybe it was a temporary glitch. If it still fails, just do the
> best you can.

Why this is wrong: it discards the specific information in the receipt (a
timeout is not a glitch — it is a refusal from an unreachable path), it re-asks
for work that was already attempted three times, and it leaves the executor to
guess which of two clearly incompatible options you meant. Expect another blocked
return, now with less information.

**Wrong response #2**

> Understood, cancelling the task.

Also wrong: the task is not the problem, and cancelling throws away everything
established (the diagnosis, the replica path, the staleness measurement). You
will pay for all of it again.

**Right response**

> Proceed against the read replica for structure and totals. Skip row-level
> checks entirely and say so explicitly in the result. I will unblock the
> security group separately.

Why this works: it *chooses* between the two options the receipt offered,
narrows the scope to what the replica can honestly support, and records the
limitation as part of the result rather than as a footnote. The next receipt is
a usable deliverable.

**Rule:** a `blocked_with_evidence` return names the next action. Take it,
change it, or decline it — but always answer *the thing it asked*.

---

## 5. Steer or cancel?

**Situation.** A task is 40 minutes into a report. You learn the audience changed
from engineers to the sales team. Nothing about the objective changed.

**Wrong move.** Cancel and re-order with the new audience.

Cost: everything produced so far, plus the fresh context of a new task, plus the
queue wait. And the executor may have already produced eight of ten sections that
survive the change.

**Right move.** One steer:

> Audience changed: this now goes to the sales team, not engineers. Keep the
> structure and the findings; rewrite the framing so a non-technical reader can
> act on it, and drop the implementation detail. Same deadline.

Cost: one round. The executor re-frames what exists instead of rebuilding it.

**When cancelling *is* right:** the goal changed, not the audience. Or the work is
acting on the wrong object. Or it must stop for cost or safety reasons. And when
you do cancel because the budget or deadline moved, prefer a **graceful wrap-up**
over an immediate stop — you get a coherent partial result instead of a pile of
intermediate state.

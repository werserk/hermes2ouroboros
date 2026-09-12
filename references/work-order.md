# The Work Order

An order is a contract, not a message. Nine fields, in this order. An empty field
is honest; a field filled with a guess is worse than empty, because the executor
will treat your guess as a fact and build on it.

The skeleton you can copy is at [`../templates/work-order.md`](../templates/work-order.md).

---

## 1. Objective

**One or two sentences. The outcome, not the steps. What must be true when this
is finished?**

The test: could two competent people disagree about whether this objective was
met? If yes, it is not yet an objective.

| Bad | Why it fails |
|---|---|
| "Look into the billing issue" | Not an outcome. No completion state exists |
| "Improve the onboarding flow" | Unbounded; any change satisfies it |
| "Fix the bug in the parser" | Which bug? Under what condition? Verified how? |

| Better |
|---|
| "Every invoice whose currency differs from the account currency renders its own symbol and its own total in the invoice list; today every row shows the account symbol." |
| "The three findings from the incident review are each traced to a root cause with a reproducible trigger, or explicitly marked `not reproducible` with the attempts listed." |

Note what the better versions do: they name the *observable* difference between
now and done. They leave the method entirely open.

## 2. Definition of done

**How someone else proves it. Name the CHECK, not the intention.**

This is the field that converts a conversation into a result. It is also the
field most often skipped, and skipping it is why results arrive that nobody can
accept or reject.

Good definitions of done are concrete and independently runnable:

- "`pytest tests/enrichment.py -q` exits 0."
- "The report answers the three questions in section 2, one dated source per
  claim, and lists `not found` with the exact query where nothing was found."
- "The artifact exists at `<path>` and opens in a browser at 1280px without a
  horizontal scrollbar."
- "The endpoint returns the same bytes for the same input on two consecutive
  calls, proven by the recorded response digests."

Bad definitions of done: "the code is clean", "it works", "quality is high",
"it looks good", "it is production-ready". Each of these delegates the acceptance
judgement back to the person who cannot make it.

**If you cannot name a check, say so on purpose.** "I do not know how to verify
this — propose a check and tell me what it would not catch" is a legitimate and
valuable order. It is a different kind of order from a specification, and the
executor will handle it differently and honestly.

Two further rules that save real money:

- **Say who runs the check.** "You verify" and "I will verify" are different
  orders. The first buys you a receipt; the second buys you an artifact you
  must still test.
- **Say what the check does NOT prove.** A check that covers the parser does not
  cover the API contract. Naming the gap prevents a verified-and-still-wrong
  delivery.

## 3. Not in scope

**What must not change, and what is deliberately deferred.**

Non-goals protect both sides. A capable executor that notices something broken
next to your task has three options: fix it (you pay to review an unrequested
change), ignore it (you may never learn), or report it (best). Say which you want.

- "Do not touch the migration files."
- "Do not refactor while you are in there — I am reviewing that area separately."
- "Changes outside `<dir>` are out of scope; report anything you find there
  instead of fixing it."
- "Deferred on purpose: authentication. It is known-broken and out of scope."

Deferred is not the same as forgotten. Writing it down is what lets the executor
stop wondering whether you already know.

## 4. Inputs and sources of truth

**Exact paths, ids, URLs — each labelled.**

| Label | Meaning |
|---|---|
| `[AUTHORITATIVE]` | Trust this and nothing else. If it disagrees with anything else, it wins |
| `[REFERENCE]` | Context. May be stale |
| `[UNVERIFIED]` | Someone claims this; check before relying on it |

Three rules:

1. **Say what you withheld and why.** If a document exists but you are not
   sending it, say so. Silence reads as "does not exist", and the executor will
   re-derive what you already have — at your expense.
2. **Say which source wins.** Two plausible sources with no precedence rule is
   the single most reliable way to buy a wrong answer.
3. **Mark staleness.** "This dump is from Tuesday; the system changed Wednesday"
   is the difference between a useful input and a trap.

## 5. Authority

**What may be changed, what is read-only, and who approves the irreversible.**

- Which files, repositories, systems, accounts.
- What is explicitly read-only.
- **Who approves:** spending money, writing to external services, publishing
  anything, contacting third parties.

Two failure modes this field prevents:

- **Overreach.** Unspecified authority is read as "probably fine" by an agent
  that wants to finish the job.
- **Stalling.** An agent that hits an unspecified boundary will stop and ask, and
  you have now spent a round granting permission you would have granted up front.

If you are not sure who approves publishing, the answer is you do. Say so.

## 6. Constraints

The cheap fields. Each one you omit is a plausible round of rework.

- **Deadline** — and whether it is hard or a target. A hard deadline changes
  strategy (the executor will drop depth to be on time); a soft one does not.
- **Budget** — and see the trap below.
- **Environment** — where it runs, what is installed, what is forbidden.
- **Language and format** of the deliverable. If you do not say, you get the
  executor's default, which may be its own working language rather than your
  reader's.
- **What must be preserved** — compatibility, existing behaviour, visual style,
  public interfaces, file layout.

> **The budget trap.** A per-task cost cap is not a safety dial you can turn down
> freely. In the reference implementation the graceful in-task stop is
> `min(a share of the remaining global budget, cap − planning_margin)`, where the
> planning margin is an **absolute** amount reserved for the wrap-up call (it is
> `$3.00` in the shipped defaults). Consequences you must plan for:
>
> - a cap **at or below** the margin resolves to *immediate wrap-up*: the task
>   finalizes having done no work, and reports that honestly;
> - a cap **just above** it (say `$3.01`) yields a ceiling of one cent — the task
>   stops after its first spend;
> - a **deadline** so tight that no review can run produces an unaccepted or
>   degraded result even when the work itself succeeded.
>
> Set a cap that can actually buy the work, and leave the margin free. If your
> platform exposes the cap as a per-task setting, check the shipped margin before
> choosing a number — a cap chosen "to be safe" is the most common way to buy
> nothing.

## 7. The decision this serves

**One sentence: what YOU will do with the result.**

This is the highest-leverage field in the whole order, and it is the one nobody
writes.

It works because it lets the executor resolve ambiguity the way *you* would,
without asking. Compare:

- "Research the three vendors." → the executor researches everything it can find;
  you wanted the one question you must answer tomorrow.
- "Research the three vendors. I must choose one by Friday and justify the choice
  to a skeptical CTO who already prefers vendor B." → the executor finds the
  comparison that survives a hostile reader, and spends nothing on the parts of
  the space you will never ask about.

The second is not longer. It is the same length and worth several rounds.

## 8. Cost of error

**What happens if this is wrong, or late.**

This is what sets how much verification and review the work deserves. Some examples:

| Cost of error | Appropriate depth |
|---|---|
| A throwaway internal note | One pass, no review |
| A decision worth a week of work | Explicit sources, a second opinion, written assumptions |
| An irreversible external action | Verification before the action, and a named approval |
| Silent data corruption | Idempotence, a dry run, and a stated rollback |

Saying "this is low-stakes, do not over-engineer it" is a real instruction and it
saves real money. So is "this is irreversible; stop before the last step".

## 9. Verification owner

**Who runs the check, with what, and what a pass buys.**

- "You run the check and record the receipt."
- "I will run it — give me the exact command."
- "Neither of us can fully verify this; give me the best proxy and tell me what
  it misses."

Naming this up front prevents the most common closing argument in agent-to-agent
work: a result that says "done" and a requester who cannot tell whether that is
true.

---

## Sizing: how much order does this need?

Not every task deserves nine fields.

| Work | Minimum viable order |
|---|---|
| A question with a knowable answer | Objective + the decision it serves |
| A small, reversible change | Objective + definition of done + not in scope |
| A deliverable someone else will read | all nine |
| Anything irreversible or externally visible | all nine, plus explicit approval and a rollback |

The rule: **the cost of writing the field is always lower than the cost of the
round it replaces.** Write it.

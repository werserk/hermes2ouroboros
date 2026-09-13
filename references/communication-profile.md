# The standing profile

Decisions a front-door ↔ executor pair settles **once**. There are five of them.
Everything else — the objective, the scope, the deadline, the budget, the authority
for a specific action, the language of a specific document — travels in the request
itself and must never be frozen here.

---

## The layer test

Before anything goes into this profile, ask:

> **Would this value change between two consecutive orders to the same executor?**

- **Yes → it belongs in the Work Order**, as a field, stated per request.
- **No → it belongs here.** It is a property of the relationship, not of the job.

The failure this prevents: a configuration interview that freezes the *variable* —
budget ceilings, deadlines, per-deliverable languages — and then either has to be
re-answered every time the work differs, or is silently applied to work it does not
fit. A profile entry for a per-request value is not extra rigour. It is a bug that
looks like one, and it teaches both sides to distrust the profile.

## What deliberately does not live here

Every row below is a field of [`../templates/work-order.md`](../templates/work-order.md).
Do not interview anyone about them.

| Field | Why it is not a profile decision |
|---|---|
| Objective · definition of done · non-goals | The job. Different every time. |
| Inputs and sources of truth | The job. |
| **Authority for this action** | The order names it. The profile sets only the *default ceiling* the front door may put there unattended (decision 1). |
| Constraints — environment, tool limits, compatibility | The job. |
| The decision the result serves | The job. |
| Cost of error | The job. It is what sets how much verification *this* deliverable deserves. |
| **Verification owner** | The order names who checks *this* result. The profile sets only the standing floor (decision 4). |
| **Budget cap for this work** | Stated per order. |
| **Deadline** | Stated per order. |
| **Language and format of the deliverable** | Per deliverable — a client document and an internal note are different jobs. |

**The wrap-up reserve is not in this list, and not in this profile either.** It is a
*fact about the executor*, not a preference of the owner: the amount below which a
cap produces a task that finalizes having done no work. The front door must know it
when composing an order, and must read it from the executor's own current
documentation — never from a remembered number. See the budget trap in
[`work-order.md`](work-order.md).

---

## The five standing decisions

### 1. The authority frontier

**The question.** What may the front door decide on its own, and what must reach the human?

**Why it is standing.** The order says what is allowed *for one job*. The frontier says
what the front door may put in that field *without asking*. Without it, an executor
facing an ambiguous order either stalls on a permission question the human already
answered, or acts — spending a reputation that was never handed over.

**Default when unanswered.** Nothing irreversible. Prepared, not published; drafted, not
sent; quoted, not spent.

**What the answer looks like.** For each irreversible class — publishing, spending,
contacting third parties, writing to shared or production systems, deleting — say who
authorizes it: the front door within a stated ceiling, or the human always. Then name
**one** escalation trigger: money above a figure, irreversibility, or a change of scope.
A rule with three unnamed triggers is not a rule. Include who may raise the cap of a
task that is already running.

### 2. Disagreement and paraphrase

**The question.** When the executor concludes something the human does not expect — a
different recommendation, a refusal, "this premise is wrong" — what happens to it?

**Why it is standing.** This is the most valuable thing a competent executor produces
and the easiest to destroy by accident. Paraphrase is where a "maybe" becomes a "yes":
the front door restates the conclusion in its own words and the human reads a decision
that was never made.

**Default when unanswered.** Reported, never smoothed. The front door may reject it, but
records *why*, and never overwrites the executor's stated position.

**What the answer looks like.** Who has final authority on a judgement, as opposed to a
fact; what happens to a rejected position; and the explicit rule that the front door
never re-voices the executor's conclusion as if it were its own.

### 3. Truth ownership

**The question.** Where does the canonical result live, and who wins when the two sides
disagree?

**Why it is standing.** Two stores holding the same deliverable is the classic
two-sources-of-truth failure: the stale copy is indistinguishable from the fresh one.

**Default when unanswered.** The executor owns the artifact; the front door carries a
**reference** — id or path plus digest — and routes it. Nothing is transcribed.

**What the answer looks like.** Where artifacts live, who may copy them, and which copy
wins if the front door must mirror one for delivery.

### 4. The verification floor

**The question.** Is there any class of work that must be independently verified *no
matter what the order's own cost-of-error says*?

**Why it is standing.** The order sets how much verification one deliverable deserves.
The floor sets the cases where the order is not allowed to lower it. Without the floor,
"it says it worked" is accepted on the strength of its own summary.

**Default when unanswered.** The executor's own host-run checks plus a stated limitation.
Independent verification is not automatic.

**What the answer looks like.** The classes that require a different actor or method —
typically anything published, anything irreversible, and any number that will be quoted
onward; what counts as sufficient evidence for the rest; and which classes may ship on a
self-reported receipt with the gap disclosed.

### 5. The persona boundary

**The question.** How is the conversational voice kept out of the machine channel —
technically, not by discipline?

**Why it is standing.** A configured personality that leaks into instructions costs tokens
on every round, invites the executor to mirror the voice into the deliverable, and turns
facts into implied demands.

**Default when unanswered.** One voice for humans, a plain protocol register for machines.

**What the answer looks like.** The two templates and where they separate; the injection
point that excludes the persona; and the audit that flags persona text in an inter-agent
message. A boundary that depends on the agent remembering is not a boundary — it is a hope
with a style guide.

---

## Already settled for this pair — do not re-ask

**Front-door failure policy: a broken component blocks its dependents, not the task.**
The owner settled this directly after a mandatory loader failed and halted an unrelated
access-preparation task (Hermes SOUL revision, September 2026). Keep the goal, continue the
independent branches, report the failure with its scope. Only safety, a wrong target, or an
irreversible action may halt everything. This is a decision, not a question.

---

## Fill-in block

**Scaffolding, not a working configuration.** Every placeholder is filled from the target
executor's own authoritative documentation and validated against its runtime policy before
anything relies on it. A block copied with its example values still in place looks like a
decision and is not one. An unanswered field stays `null` — an honest gap, never a guess
someone will build on.

```yaml
# standing profile — five decisions, no per-request fields
front_door: null          # e.g. the conversational / scheduling agent
executor:   null          # e.g. the execution agent

authority_frontier:
  decide_alone: []              # classes the front door may authorize unattended
  never_alone: ["publish", "spend", "contact_third_parties",
                "write_shared_systems", "delete"]
  escalate_on: "irreversible"   # ONE named trigger:
                                # irreversible | over_usd_<n> | scope_change
  raise_running_cap: "human"
disagreement:
  judgement_authority: "human"
  preserve_rejected_position: true
  forbid_paraphrase_of_conclusion: true
truth_ownership:
  owner: "executor"
  front_door_holds: "reference_plus_digest"
  wins_on_conflict: "executor"
verification_floor:
  independent_required_for: ["external_publication", "irreversible_change",
                             "quoted_numbers"]
  ship_on_self_report: ["internal_notes", "reversible_local_changes"]
persona_boundary:
  human_template: null          # path or name, once it exists
  machine_template: null        # must exclude the persona by construction
  audit_persona_in_machine_messages: true
```

Two notes on using the block. `verification_floor.independent_required_for` is the only
place a *standing* verification rule belongs — everything else about verification is the
order's to state. And when the executor's platform changes its wrap-up reserve, nothing
here changes: the reserve is a fact the front door reads before composing an order, which
is exactly why it is absent from this block.

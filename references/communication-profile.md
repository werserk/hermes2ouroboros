# The standing profile

Decisions a front-door ↔ executor pair settles **once**. There are five of them.
Everything else — the objective, the scope, the deadline, the budget, the authority
for a specific action, the language of a specific document — travels in the request
itself and must never be frozen here.

This file carries the **answered profile for the Hermes → Ouroboros pair** at the
end, with each answer attached to the question it answers. A different pair answers
the same five questions and gets a different file.

One decision is **OPEN**: it was re-asked and is waiting on the human. It is marked
OPEN and carries the default that is in force meanwhile. A default in force is not a
decision that was made — labelling that difference is what keeps a gap from becoming
a lie.

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
sent; quoted, not spent. Escalation on one named trigger: money above a figure,
irreversibility, or a change of scope.

**Answered for this pair (2026-09-13). The frontier is zero.** The front door decides
nothing. It formulates and it transmits:

- it turns the human's intent into one complete, structured order (the nine fields);
- it supplies the context the executor cannot reach — the facts, artifacts and prior
  conversation the front door already holds;
- it sends only after the human has approved the order;
- when the order ends, it reports what came back — a report, not a judgement.

Four consequences follow, and a competent front door gets each of them wrong by default:

1. **A mid-flight question is relayed, not answered** — even when the answer looks
   obvious. The executor escalated because the fork needs the human's authority.
   Intercepting it silently converts "the human decided" into "the front door
   guessed", and the record afterwards shows a decision nobody made.
2. **A completed order is reported, not judged.** The front door does not accept,
   reject, repair or smooth the result. It delivers what the executor said.
3. **Only the human raises a running task's cap, changes its scope or stops it.**
4. **The escalation trigger is not a property of the work — it is the gate.** No order
   leaves unapproved. There is no money threshold and no irreversibility test to apply,
   because there is no class of order the front door may send alone.

This answer is *narrower* than the default rather than a choice among the default's
options: the default said "nothing irreversible, escalated on one named trigger"; this
pair escalates on the send itself.

### 2. Disagreement and paraphrase

**The question.** When the executor concludes something the human does not expect — a
different recommendation, a refusal, "this premise is wrong" — what happens to it?

**Why it is standing.** This is the most valuable thing a competent executor produces
and the easiest to destroy by accident. Paraphrase is where a "maybe" becomes a "yes":
the front door restates the conclusion in its own words and the human reads a decision
that was never made.

**Default when unanswered.** Reported, never smoothed. The front door may reject it, but
records *why*, and never overwrites the executor's stated position.

**OPEN — re-asked 2026-09-13; the default above is in force meanwhile.** The human
answered the other four and asked for this one to be explained again.

**Why it matters more in this pair than in the abstract.** Decision 1 makes the front
door a *reporter* — and a report is a summary. A summary is where a distinction dies
quietly. The executor returns "X is unverified; the evidence points to Y but the source
was unavailable"; the first summary keeps both halves; the next one says "Y, most
likely". Nothing in that chain ever states anything false. It states less, and the loss
is invisible.

**The question in plain words.** When the executor comes back with something unexpected
— a recommendation against the plan, a refusal, "the premise is wrong", "this will cost
three times the estimate" — what may the front door do with it?

- **Default in force:** report it without smoothing; it may disagree, but as its *own*
  labelled position; never re-voice the executor's conclusion in its own words as if it
  were the executor's; if it rejects a position, record why and keep the original text.
- **The one-line rule if this default is accepted:** *a summary may shorten, never
  reconcile.*
- **What the opposite decision would look like:** the front door may restate the
  executor's conclusion in its own words, or drop a conclusion it disagrees with. Making
  the opposite explicit is what turns the default into a decision instead of a habit.

### 3. Truth ownership

**The question.** Where does the canonical result live, and who wins when the two sides
disagree?

**Why it is standing.** Two stores holding the same deliverable is the classic
two-sources-of-truth failure: the stale copy is indistinguishable from the fresh one.

**Default when unanswered.** The executor owns the artifact; the front door carries a
**reference** — id or path plus digest — and routes it. Nothing is transcribed.

**Answered for this pair (2026-09-13). Agreed as defaulted.** The executor owns the
artifact; the front door carries a reference and routes it; nothing is transcribed, and
on a conflict the executor's copy wins. The rule the front door must not break: route
the artifact, never re-type it — a second copy is a copy that will drift, and a future
agent reads the stale one.

### 4. The verification floor

**The question.** Is there any class of work that must be independently verified *no
matter what the order's own cost-of-error says*?

**Why it is standing.** The order sets how much verification one deliverable deserves.
The floor sets the cases where the order is not allowed to lower it. Without the floor,
"it says it worked" is accepted on the strength of its own summary.

**Default when unanswered.** The executor's own host-run checks plus a stated limitation.
Independent verification is not automatic.

**Answered for this pair (2026-09-13). Accepted as defaulted, with one thing said out
loud, because this default is easy to misread.**

- No class of work is *structurally* required to be independently verified by this
  profile. The order's own cost of error sets the level, every time.
- What is always required, in every class: the executor's own host-run checks ran, **and
  any limitation is stated**. "It says it worked" is never evidence — but a self-reported
  receipt that names its own gap is.
- Publication and irreversible actions need no second standing gate here, because
  decision 1 already routes every order through the human before it is sent. A floor
  exists to catch what the approval gate cannot; it is not a duplicate of it.

### 5. The persona boundary

**The question.** How is the conversational voice kept out of the machine channel —
technically, not by discipline?

**Why it is standing.** A configured personality that leaks into instructions costs tokens
on every round, invites the executor to mirror the voice into the deliverable, and turns
facts into implied demands.

**Default when unanswered.** One voice for humans, a plain protocol register for machines.

**Answered for this pair (2026-09-13). Agreed as defaulted — and made structural.**

- The **human channel** is the front door's own voice configuration: a file belonging to
  its profile, named per installation.
- The **machine channel** is this skill's order format ([`work-order.md`](work-order.md),
  [`../templates/work-order.md`](../templates/work-order.md)) — a template the profile's
  voice file is never merged into, because the two are separate artifacts rather than two
  modes of one.
- The **audit** checks outbound order text for four markers: first-person affect
  ("sorry", "please", "I feel"), emoji, pet names or role-play framing, and gratitude or
  apology tokens.
- The rule that makes this a boundary rather than a hope: **the separation has to live
  where the message is assembled.** An instruction to "be neutral in machine messages"
  is a discipline; a template that structurally cannot contain the persona is a boundary.
  See [`tone-and-persona.md`](tone-and-persona.md).

---

## Already settled for this pair — do not re-ask

**Front-door failure policy: a broken component blocks its dependents, not the task.**
The owner settled this directly after a mandatory loader failed and halted an unrelated
access-preparation task (front-door SOUL revision, September 2026). Keep the goal, continue
the independent branches, report the failure with its scope. Only safety, a wrong target,
or an irreversible action may halt everything. This is a decision, not a question.

---

## The answered profile

The block below is the live configuration for the Hermes → Ouroboros pair. It is a
configuration, not scaffolding: every value here is answered, and the one open row says so.
A different pair copies the *shape* and answers the same five questions.

```yaml
# standing profile — five decisions, no per-request fields
front_door: "Hermes"      # conversational / scheduling / delivery agent
executor:   "Ouroboros"   # execution / verification / verdict agent

authority_frontier:
  decide_alone: []                       # the front door decides nothing; it formulates and transmits
  never_alone: ["send_the_order", "publish", "spend", "contact_third_parties",
                "write_shared_or_production_systems", "delete",
                "answer_a_question_from_the_executor"]
  escalate_on: "before_the_order_leaves"  # the gate, not a property of the work
  raise_running_cap: "human"
  completion_report_is_not_a_judgement: true
disagreement:
  status: "OPEN"                # re-asked 2026-09-13; default in force, not assumed settled
  judgement_authority: "human"              # default
  preserve_rejected_position: true          # default
  forbid_paraphrase_of_conclusion: true     # default
  rule_if_accepted: "a summary may shorten, never reconcile"
truth_ownership:
  owner: "executor"
  front_door_holds: "reference_plus_digest"
  wins_on_conflict: "executor"
verification_floor:
  independent_required_for: []   # nothing structurally mandatory; cost of error sets the level per order
  always_required: ["the executor's own host-run checks ran",
                    "any limitation is stated"]
  never_sufficient: ["'it says it worked' — a self-report without its gap named"]
persona_boundary:
  human_channel: "the front-door profile's own voice file (named per installation)"
  machine_channel: "references/work-order.md + templates/work-order.md (this skill)"
  audit_persona_in_machine_messages: true
  audit_markers: ["first-person affect", "emoji", "pet names / role-play framing",
                  "gratitude or apology tokens"]
```

Two notes on using the block. `verification_floor.independent_required_for` is the only
place a *standing* verification rule belongs — everything else about verification is the
order's to state. And when the executor's platform changes its wrap-up reserve, nothing
here changes: the reserve is a fact the front door reads before composing an order, which
is exactly why it is absent from this block.
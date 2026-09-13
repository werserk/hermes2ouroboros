# The communication profile

Ten decisions that configure a front-door ↔ executor pair. Answer them **once**,
write the answers down, and both sides stop re-deciding them under pressure.

Each entry states why it matters, the behaviour if you never answer, and what the
answer should look like when written down. The defaults are deliberately
conservative: they are safe for a pair that has not yet built trust. Tighten or
loosen them deliberately, not by accident.

Fill in the block at the end and keep it next to this skill (or in the front
door's profile). It is the difference between a contract that exists and one that
is applied.

---

## 1. Authority ceiling

**Why.** The single most consequential ambiguity in agent-to-agent work. An
executor that cannot tell whether it may publish will either stop (wasting a
round asking) or act (spending your reputation).

**Default if unanswered.** Nothing irreversible without the human. Locally
prepared, never published; reported, never sent; drafted, never spent.

**What to write.** For each irreversible class — publishing, spending money,
contacting third parties, writing to shared or production systems, deleting
anything — say who authorizes it: the front door within a stated ceiling, or the
human always.

## 2. Escalation threshold

**Why.** Determines how often the human is interrupted. Too low and the human
becomes a bottleneck and stops reading; too high and decisions get made silently.

**Default if unanswered.** Escalate anything irreversible or anything that changes
the *scope* of the work. Answer everything else from the front door's own
judgement.

**What to write.** The rule the front door applies when deciding whether to decide
itself: a money threshold, an irreversibility test, a scope test. Say explicitly
which of those three is the trigger.

## 3. Deliverable ownership

**Why.** Two stores holding the same result is the classic two-sources-of-truth
failure; the stale copy is indistinguishable from the fresh one.

**Default if unanswered.** The executor owns the artifact; the front door carries a
**reference** (id/path plus digest) and routes it. Nothing is transcribed.

**What to write.** Where artifacts live, who may copy them, and — if the front door
must mirror one for delivery — which copy wins when they disagree.

## 4. Disagreement protocol

**Why.** The executor will sometimes conclude something the front door does not
expect: a different recommendation, a refusal, a "this premise is wrong". This is
the most valuable thing a competent executor produces, and the easiest to
suppress by accident.

**Default if unanswered.** The disagreement is reported, never smoothed. The
front door may reject it, but must record *why* and must not overwrite the
executor's stated position.

**What to write.** Who has final authority on a *judgement* (as opposed to a fact),
and what happens to the rejected position. Rule to avoid: never let the
front door restate the executor's conclusion in its own words as if it were the
executor's — paraphrase is where a "maybe" becomes a "yes".

## 5. Language and register of deliverables

**Why.** Unspecified, you get the executor's working language rather than your
reader's. Specified late, you pay for a rewrite.

**Default if unanswered.** The executor's own working language, in a neutral
declarative register.

**What to write.** The default language for deliverables and for the machine
channel, and any per-class exceptions (for example: prose for humans in the
owner's language; code, commits and identifiers in English). Note that register is
separate from language — see [`tone-and-persona.md`](tone-and-persona.md).

## 6. Budget posture

**Why.** A per-task cap is not a free safety dial. A cap at or below the platform's
wrap-up reserve produces a task that finalizes having done no work — see the
budget trap in [`work-order.md`](work-order.md). Getting this wrong is the most
expensive single mistake available.

**Default if unanswered.** No per-task cap; the global budget and the executor's
own in-task ceiling apply.

**What to write.** The standing per-task working cap; the absolute floor below
which a cap must never be set (know the platform's reserve — it is an *absolute*
amount, not a percentage); and who may raise it mid-task.

## 7. Deadline posture

**Why.** A hard deadline changes strategy: the executor drops depth to be on time.
That is correct, and it must be a conscious choice rather than an accident. A
deadline with no room for the verification you asked for produces an unaccepted
result even when the work succeeded.

**Default if unanswered.** Soft. State quality-versus-time priority explicitly when
they conflict.

**What to write.** Whether deadlines are hard or targets by default; whether a
deadline may ever buy down verification (recommended: only by explicit
instruction); and the minimum review window you insist on reserving.

## 8. Verification bar

**Why.** Distinguishes "it says it worked" from "it is verified". Without a stated
bar, every result is accepted on the strength of its own summary.

**Default if unanswered.** The executor's own host-run checks plus a stated
limitation. Independent verification is not automatic.

**What to write.** Which classes of work require independent verification (a
different actor or method, not a self-report); what counts as sufficient evidence
for the rest; and which classes may ship on a self-reported receipt with a
disclosed gap.

## 9. Front-door failure policy

**Why.** If the front door stops the whole task when one tool, hook or dependency
fails, it converts a local, recoverable refusal into a total loss — including the
independent branches that were about to succeed. This is usually a defect in the
front door's own configuration, not a property of the executor.

**Default if unanswered.** Block only the dependent branch; preserve the goal;
continue independent work; report the failure with its scope.

**What to write.** The explicit rule, phrased so it cannot be misread: a broken
component blocks *its dependents*, not *the task*. Name what must always continue
and what may legitimately halt everything (safety, a wrong target, an
irreversible action).

## 10. Persona boundary

**Why.** A configured personality that leaks into machine instructions costs tokens
on every round, invites the executor to mirror the voice into the deliverable, and
turns facts into implied demands. It must be enforced structurally, not by
discipline.

**Default if unanswered.** One voice for humans, a plain protocol register for
machines.

**What to write.** How the two registers are kept apart **technically** — separate
templates, an injection point that excludes the persona, and a periodic audit that
flags persona text in an inter-agent message. A boundary that relies on the agent
remembering is not a boundary.

---

## Fill-in block

**This is scaffolding, not a working configuration.** Every placeholder must be
filled from the target executor's own authoritative documentation and validated
against its runtime policy before anything relies on it. A block copied with its
example values still in place is worse than no block at all: it looks like a
decision and is not one.

```yaml
# communication-profile
# front-door: <name>          # e.g. the conversational / scheduling agent
# executor:   <name>          # e.g. the execution agent
authority:
  publish:        "human"          # human | front_door
  spend:          "human_over_usd_<n>" # below the threshold the front door may decide
  contact_third_parties: "human"
  write_shared_systems: "human"
escalate_when: ["irreversible", "scope_change", "over_usd_<n>"]
deliverable_ownership:
  owner: "executor"
  front_door_holds: "reference_plus_digest"
  wins_on_conflict: "executor"
disagreement:
  judgement_authority: "human"
  preserve_rejected_position: true
language:
  deliverable_default: "<owner language>"       # e.g. "ru" for a Russian-speaking owner
  machine_channel: "<protocol language>"        # e.g. "en"
  code_and_commits: "en"
budget:
  per_task_cap_usd: null           # null = no per-task cap; a finite global budget still
                                   # derives one. With NO finite budget anywhere the in-task
                                   # cost stop is DISABLED — so state a number rather than
                                   # trusting a safe-looking default
  absolute_floor_usd: "platform_defined"   # the EXECUTOR's wrap-up reserve, from its own docs
                                           # (3.0 USD for the Ouroboros reference implementation);
                                           # never set a per-task cap at or below this
  raise_authority: "human"
deadline:
  default: "soft"
  may_buy_down_verification: false
  minimum_review_window_min: "platform_defined"
verification:
  independent_required_for: ["external_publication", "irreversible_change", "published_numbers"]
  self_reported_ok_for: ["internal_notes", "reversible_local_changes"]
front_door_failure_policy:
  blocks: "dependents_only"
  halts_everything: ["safety", "wrong_target", "irreversible_action"]
persona_boundary:
  human_template: true
  machine_template_excludes_persona: true
  audit_persona_in_machine_messages: true
```

Two notes on using the block: it is a **starting template**, so delete the keys that
do not apply rather than leaving a plausible-looking default in place — an
unanswered field is honest, a guessed one is a fact someone will build on. And
re-answer item 6 whenever the platform's reserve changes, because a cap that was
safely above the line can silently fall below it.

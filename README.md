# hermes2ouroboros

**The agent-to-agent work-order contract.** How a front-door agent should
commission work from an execution agent, read the result back, and avoid the
patterns that make two capable agents expensive.

Written for **Hermes → Ouroboros**. It applies to any pair where one agent owns a
conversation, a schedule and a delivery surface, and the other owns planning,
verification and the verdict on its own work.

The thesis in one line: **you will spend more on ambiguity than on intelligence.**

## What is in here

This is a Hermes-style skill:
`SKILL.md` is the context-resident part, and `references/` is read on demand.
That split is deliberate — the entry file is short because it is paid for on every
round, and the depth is one hop away when you need it.

| File | Read it when |
|---|---|
| [`SKILL.md`](SKILL.md) | Always — it is the contract in one page |
| [`references/agent-specificity.md`](references/agent-specificity.md) | The pairing map: what each side uniquely knows, and what never to send |
| [`references/work-order.md`](references/work-order.md) | Writing an order of any size |
| [`references/transport-adapter.md`](references/transport-adapter.md) | Building or verifying the connection: the seven required operations, identity, failure classes |
| [`references/receipt-and-verification.md`](references/receipt-and-verification.md) | A result came back and you must judge it |
| [`references/conversation-discipline.md`](references/conversation-discipline.md) | Work is in flight; you want to check, correct or stop it |
| [`references/anti-patterns.md`](references/anti-patterns.md) | Something feels inefficient and you want the name for it |
| [`references/tone-and-persona.md`](references/tone-and-persona.md) | Configuring how one agent writes to another |
| [`references/answer-contract.md`](references/answer-contract.md) | Writing the return, or judging its quality (accuracy / completeness / structure) |
| [`references/why-this-shape.md`](references/why-this-shape.md) | The architecture behind the rules, and how this relates to A2A and MCP |
| [`references/communication-profile.md`](references/communication-profile.md) | The five standing decisions — answered for this pair, with one still open |
| [`templates/work-order.md`](templates/work-order.md) | Copy-paste skeleton for an order |
| [`examples/before-and-after.md`](examples/before-and-after.md) | Five worked examples, good and bad |

## Install

A skill is a directory. Ask the platform where a profile's skills live instead of
assuming a path, then copy the directory in under a category:

```sh
PROFILE="$(hermes profile show <profile-name> | sed -n 's/^Path: *//p')"
mkdir -p "$PROFILE/skills/autonomous-ai-agents"
cp -a hermes2ouroboros "$PROFILE/skills/autonomous-ai-agents/"
hermes skills list | grep hermes2ouroboros      # verify with the loader, not with ls
```

`HERMES_HOME` selects the profile root for a command that carries no profile flag.

Two things worth knowing before you call it installed:

- **A directory that exists and a skill that loads are different facts** — only the
  loader answers the second. `hermes profile show <name>` prints a skill *count*,
  which is the cheapest way to watch the number move.
- **Curators archive things.** Hermes's curator prunes stale agent-created skills
  (30 days unused → stale, 90 → archived, archives recoverable). A skill with **no
  provenance marker** shows up in `hermes curator list-unmanaged` and is *never*
  auto-staled or auto-archived — the state you want for a relationship contract that
  has to outlive a quiet month. `hermes curator adopt <name>` hands it to the curator,
  and the staleness rules then apply to it. Choose deliberately: adopting is not
  "more correct", it is a different lifetime.

## The contract in one screen

**You send** a Work Order: objective · definition of done (the actual check) ·
not in scope · inputs with a named winner · authority and approvals · constraints
(deadline, budget, language, format) · **the decision the result serves** · cost of
error · who verifies.

**You get back** one of three honest tiers:

- `solved` — verified against the task's own surface; worth reading the evidence;
- `best_effort` — a real deliverable with named gaps; **not** a failure;
- `blocked_with_evidence` — could not proceed, cause and next action given; the
  most valuable return there is, because it arrives before money is wasted.

**You obey seven rules:** one unit of work = one complete order · if it cannot be
checked it is not done · state the outcome, not the choreography · a typed refusal
is a verdict, not an outage · steer before you cancel, wrap up before you stop ·
the machine channel is a protocol, not a personality · give it only what it cannot
know.

## Install

Copy this directory into a Hermes skills tree — either the home-wide tree or a
specific profile's:

```
<hermes-home>/skills/autonomous-ai-agents/hermes2ouroboros/          # all profiles
<hermes-home>/profiles/<profile>/skills/autonomous-ai-agents/hermes2ouroboros/   # one profile
```

It is markdown-only: no scripts, no dependencies, no permissions to grant. If your
Hermes build maintains a skill index or hub manifest, re-index after copying.

### Composing with an installation-specific operator skill

This skill is deliberately **generic** — it contains no hosts, paths, credentials
or deployment facts, so it can be shared and published.

Those belong in a separate, installation-specific operator skill that knows your
deployment's actual API surface (the concrete endpoints, the account layout, the
model configuration). The split is intentional and worth keeping:

| Layer | Contains | Shareable |
|---|---|---|
| This skill | *How to talk* — the contract, cadence, verification discipline | yes |
| Your operator skill | *How to reach* — endpoints, hostnames, accounts, model slots | no |

Where the two meet, [`references/why-this-shape.md`](references/why-this-shape.md)
lists the capabilities an order needs (admission, result read, live status,
steering, cancel-with-policy, decision ingress, artifact reference) so you can map
them onto whatever names your deployment uses.

## A verifiable claim

The cost guidance in this skill is not folklore. The budget trap it warns about —
a per-task cap that guarantees the task finalizes without doing any work — is
verifiable in the reference implementation's own source:

- `COST_PLANNING_MARGIN_USD = max(1.0, 2.0 * 1.50)` → **`$3.00`**, an *absolute*
  reserve, not a percentage;
- a root cap at or below it resolves to `exhausted_soft_land`
  (basis `root_cap_at_or_below_planning_margin`) — immediate best-effort wrap-up;
- a cap just above it yields a ceiling of pennies.

The wider point of the skill is that this kind of fact — *what the floor actually
is* — belongs in the order, and that facts about the floor are the ones worth
verifying in source rather than trusting from a summary.

## Scope and honesty

This is opinionated engineering guidance, not a specification of any particular
product, and it is written from the perspective of the execution side of the pair.

- It does not claim to describe every agent platform. It describes the shape that
  follows when an executor owns its own planning and verification.
- Where it states a number or a mechanism, that number comes from the reference
  implementation and is marked as such.
- It deliberately contains no deployment-specific detail, so nothing here goes
  stale when an installation changes.

If you find a rule that does not hold in your setup, the reasoning in
`references/why-this-shape.md` is written so you can re-derive the rule instead of
inheriting it.

## Related standards

This document is deliberately a **contract**, not a wire protocol. It says what a
message should contain and what a return must prove; it does not say how the bytes
travel or how an agent is discovered.

Those are separate problems with separate solutions. If you need discovery, a
standard task lifecycle, or push delivery between parties that do not share a
codebase, an open standard such as [A2A](https://github.com/a2aproject/A2A)
provides the envelope — MCP plays the same role for agent-to-tool connections.

The two layers are complementary rather than competing: **the wire carries the
message; this contract decides what the message says.** The mapping between A2A's
task states and the vocabulary used here is in
[`references/why-this-shape.md`](references/why-this-shape.md).

## Verifying this repository

The check that matters for a published skill is that **a stranger with no
credentials can read exactly what was intended**. That is one clone and one
script, and it needs no auth:

```sh
git clone https://github.com/<owner>/hermes2ouroboros /tmp/h2o-check && cd /tmp/h2o-check
python3 - <<'PY'
import pathlib, re
root = pathlib.Path('.').resolve()
files = sorted(p for p in root.rglob('*') if p.is_file() and '.git' not in p.parts)
md = [f for f in files if f.suffix == '.md']
refs = sorted(p.name for p in (root / 'references').glob('*.md'))
targets = refs + ['templates/work-order.md', 'examples/before-and-after.md']
bad = []
for f in md:
    for m in re.finditer(r'\]\(([^)]+)\)', f.read_text(encoding='utf-8')):
        t = m.group(1)
        if t.startswith(('http://', 'https://', '#', 'mailto:')):
            continue
        t = t.split('#')[0].strip()
        if t and not (f.parent / t).resolve().exists():
            bad.append(f"{f.relative_to(root)} -> {t}")
print(f"files={len(files)} md={len(md)} broken_links={len(bad)}")
for name in ('SKILL.md', 'README.md'):                      # each entry surface, separately
    text = (root / name).read_text(encoding='utf-8')
    missing = [t for t in targets if t not in text]
    print(f"{name}: unreached={missing or 'none'}")
print("links_ok =", not bad)
PY
```

Expected: `broken_links=0`, and `unreached=none` for **both** entry surfaces
independently. Reporting the union is not enough — a reference reachable only
from the README is unreachable for a reader who arrived through `SKILL.md`.

Three honest notes on what this does and does not establish:

- It verifies **structure and reachability**, which is what a markdown skill can
  be wrong about mechanically. It cannot verify that the *advice* is good; that
  is a judgement, and it is meant to be argued with.
- It must be run against the **published remote**, not your local checkout. A
  local pass says nothing about what actually landed.
- The file counts in this README are deliberately not restated as numbers here;
  a count written into documentation goes stale, and the script prints the live
  one.

## License

MIT. See [`LICENSE`](LICENSE).

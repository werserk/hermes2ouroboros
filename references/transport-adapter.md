# The transport adapter

[`work-order.md`](work-order.md) says **what** to send.
[`answer-contract.md`](answer-contract.md) says **what must come back**.
This file says **which operations must exist** for either to be possible — and
with what identity and failure semantics — so an integrator can build or verify a
real connection instead of inventing endpoint semantics.

It is deliberately transport-agnostic. Whether the operations are HTTP paths,
JSON-RPC methods, a message bus or an A2A binding is the wire's business
(see the A2A relation in [`why-this-shape.md`](why-this-shape.md)).

## The seven operations

| # | Operation | Must accept | Must return | Identity rule |
|---|---|---|---|---|
| 1 | `admit` | the work order, plus an **idempotency key** | a durable **task id**, or a typed refusal | same key + same payload → rejoin the existing task; same key + different payload → **refuse** |
| 2 | `read` | a task id | status, tier, answer, evidence, gaps, artifact references, cost and cost finality | read-only; never mutates |
| 3 | `watch` | a task id (or a subscription) | terminal and `input-required` notifications, at-least-once, each carrying the id | notifications may repeat; the consumer must be idempotent |
| 4 | `steer` | a task id + a message | accepted / refused, with a reason | accepted only while non-terminal; **typed refusal while a stop is pending** |
| 5 | `cancel` | a task id + a **policy** (`immediate` \| `wrap_up`) | `confirmed` \| `requested` \| `failed` \| `unknown` | never claim more than is proven; a late natural completion wins |
| 6 | `answer` | a question id + the answer | delivered / already-resolved / rejected | answers a live question; must not restart the task |
| 7 | `artifact` | an artifact reference | immutable bytes (or a redirect to them), with a digest | the reference must not later resolve to different bytes |

If your executor exposes fewer than these, the contract degrades in a specific,
nameable way rather than silently — and each gap should be written down. The two
that are load-bearing for cost are `watch` (without it you will poll) and
`cancel` with a policy (without it you cannot harvest sunk work).

## Machine-readable skeleton

```json
{
  "adapter": "1",
  "operations": {
    "admit":  {"input": ["contract", "idempotency_key"],
               "output": ["task_id", "state"],
               "refusals": ["policy_refusal", "validation_error", "quota_exhausted"]},
    "read":   {"input": ["task_id"],
               "output": ["state", "tier", "answer", "evidence", "gaps",
                          "artifacts", "cost", "cost_final"]},
    "watch":  {"input": ["task_id"],
               "output": ["task_id", "event", "at_least_once"],
               "events": ["terminal", "input_required", "progress"]},
    "steer":  {"input": ["task_id", "message"],
               "output": ["accepted"], "refusals": ["terminal", "stop_pending"]},
    "cancel": {"input": ["task_id", "policy"],
               "policies": ["immediate", "wrap_up"],
               "output": ["confirmed", "requested", "failed", "unknown"]},
    "answer": {"input": ["question_id", "answer"],
               "output": ["delivered", "already_resolved", "rejected"]},
    "artifact": {"input": ["artifact_ref"],
                 "output": ["bytes", "sha256", "immutable"]}
  },
  "identity": {"minted_at": "admit", "retry_carries": "same_key",
               "changed_payload_same_key": "refuse"},
  "tiers": ["solved", "best_effort", "blocked_with_evidence"]
}
```

## Identity and idempotency: the rules that prevent double work

These four rules are the whole reason an adapter needs identity at all. Get them
wrong and you will run the same work twice, concurrently, against the same target.

1. **Identity is minted at admission** and carried unchanged by every later
   operation. It is the handle for reading, steering, cancelling and explaining.
2. **A retry carries the same key.** That is what lets a correct executor rejoin
   the original rather than start a second one.
3. **An unconfirmed admission is read before it is resent.** A lost reply is not
   evidence that nothing was accepted.
4. **A changed payload under a reused key must be refused.** A correct
   implementation refuses it as ambiguous; a sloppy one silently runs the new
   content under the old record, which is worse than either outcome.

## Failure classes

A closed set, because collapsing these into "it failed" is what destroys recovery
logic. Each has exactly one required handling.

| Class | Means | Required handling |
|---|---|---|
| `policy_refusal` | deterministic verdict on *this* request | Do not retry. Change the request |
| `validation_error` | the request is malformed | Fix it and send a **new** request |
| `unavailable` | transient transport or infrastructure failure | Wait, then retry the **same** identity |
| `quota_exhausted` | a spending or rate limit, usually with a reset time | Wait until the reset; do not hot-loop |
| `ambiguous_delivery` | the request may have been received; no terminal fact exists | **Never resend.** Reconcile by reading |
| `not_found` | no such id *at this endpoint* | Verify the identity and the endpoint before concluding anything |

`ambiguous_delivery` is the one that hurts. It is also the one a naive
implementation reports as a plain failure, which is how duplicate work starts.

## States, tiers, and why they are two axes

Two facts that are routinely conflated:

- **Lifecycle state** — `working`, `input_required`, `completed`, `cancelled`,
  `failed`. Did the machinery run?
- **Outcome tier** — `solved`, `best_effort`, `blocked_with_evidence`. Did the
  objective get met?

A task can complete its lifecycle and still not meet its objective; a task can be
cancelled and still have produced a usable partial deliverable. An adapter that
reports only "completed / failed" destroys the information the front door needs to
decide what to do next — which is why the tiers are a separate field in `read`
rather than a synonym for success.

## Mapping to A2A

If you are putting this on A2A, the binding is close to direct:

| Operation | A2A |
|---|---|
| `admit` | `message/send` (or `message/stream`) returning a task |
| `read` | `tasks/get` |
| `watch` | `tasks/resubscribe` streaming, or the push-notification configuration |
| `steer` | a further `message/send` on the same task id |
| `cancel` | `tasks/cancel` (`wrap_up` is a local policy, not an A2A concept) |
| `answer` | a further `message/send` while the task is in `input-required` |
| `artifact` | the artifact parts on the task, fetched by reference |

Note the one asymmetry: A2A has no *policy* on cancel, and no notion of the three
tiers. Those are contract-level concerns that ride on top — which is the point of
keeping the contract separate from the wire.

## What this file deliberately does not define

Authentication, transport, discovery, persistence, and retry timing. Those belong
to the wire and to the deployment. Specifying them here would make this document
stale the moment a protocol version moved — which is exactly the failure this
layer exists to avoid.

## Conformance checklist

Use this to verify an integration, or to judge one you were handed:

- [ ] Does `admit` return a **durable** id, and is that id the same thing `read`,
      `steer` and `cancel` accept?
- [ ] Is identity **idempotent** — same key rejoins, changed payload refuses?
- [ ] Does `read` expose the **tier** separately from the lifecycle state?
- [ ] Does `read` carry cost **finality**, not just a number? (A number without
      finality will be treated as a total.)
- [ ] Does `cancel` distinguish `confirmed` from `requested` and `unknown`?
- [ ] Does `cancel` accept a policy, so a stopped task can still yield a partial
      deliverable?
- [ ] Does `watch` exist, so the front door can avoid polling?
- [ ] Are ambiguous outcomes (`ambiguous_delivery`) distinguishable from plain
      failures?
- [ ] Do artifact references resolve to **immutable** bytes?

Nine questions. A "no" on any of the first four means the integration will waste
real money; a "no" on the last three means it will waste real time.

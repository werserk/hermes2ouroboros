# The answer contract

Every order needs a *return* specification, and it is not enough to say "tell me
what happened". A return is consumed twice: by a human who wants the truth, and
by the front door, which is a machine that must **decide** from it — route the
artifact, choose the next message, or stop.

That second consumer is why structure is an economic requirement rather than a
style preference. An unstructured answer forces the front door to re-derive the
state by reading everything, which is precisely the round the contract exists to
save. **A complete-but-unstructured return costs about as much as an incomplete
one**, because the parsing cost simply moves to the other side.

Three properties, in priority order: **accuracy**, **completeness**, **structure**.

---

## 1. Accuracy

Accuracy is not "being right". It is **making your claims falsifiable and your
uncertainty visible**.

### Every load-bearing claim carries provenance

Label each claim with where it came from and how strongly it is held:

| Label | Means |
|---|---|
| `observed` | Seen directly; the anchor is given |
| `derived` | Computed from observed inputs; the inputs and method are named |
| `inferred` | A conclusion the reader should be able to re-derive or reject |
| `unverified` | Asserted by a source but not checked here |
| `stale` | Was true of a state that has since moved |

A claim with no label is not neutral — it will be read as `observed`, which is
how a guess becomes a fact.

### Give the anchor, not just the conclusion

"Performance improved" is an assertion. "p95 fell from 840 ms to 210 ms over the
same 10k-request fixture; the raw samples are at `<path>`" is evidence, because
the reader can re-run it and fail to reproduce it.

Rule of thumb: **a claim is evidence when the reader can falsify it with the
information you supplied.** Anything else is a press release.

### Distinguish four things people routinely collapse

1. **"I verified X"** — a check ran, and here is what it proved.
2. **"X is true"** — a conclusion, possibly resting on an unverified premise.
3. **"The tool reported X"** — a report. Tools lie, time out, and pass vacuously.
4. **"X was not found"** — a result of a named search. Say the query, and say
   where you looked. An unnamed absence is indistinguishable from not looking.

### State what the check does *not* prove

Every check has a boundary, and the boundary is where the reader's decision
usually lives. A green suite proves the suite; it says nothing about the contract
it does not cover. Naming the gap is not weakness — it is the part of the receipt
that prevents a correct result being used wrongly.

### Never smooth over uncertainty

Hedging that hides a gap is worse than a stated gap. "Probably fine" costs the
reader the ability to act; "the totals reconcile, but the FX rates are from a
cached snapshot 6 hours old and I could not refresh them" tells them exactly what
to do next.

---

## 2. Completeness

Completeness is not "everything that could be said". It is **nothing silently
omitted**.

### An omission must be named, counted, and resolvable

Three requirements, and all three are needed:

- **named** — what was left out, specifically;
- **counted** — how much: `23 of 240 rows omitted`, not "some rows";
- **resolvable** — a reference by which a reader can get the full material
  (a path, an id, a digest). A marker that says "truncated" and stops is a
  disclosure, not a source.

This is the difference between an honest partial answer and a misleading
complete-looking one. The first is usable; the second is a trap, because the
reader cannot tell which sentence was built on the missing part.

### Coverage is a fact, not a feeling

Say what fraction of the input space you actually examined and how you chose it.
"I reviewed all 240 files" and "I reviewed the 40 files that the index flagged as
changed" are different claims, and only one of them is usually true.

### `not found` is a result

When the answer is that something does not exist, that is a finding — with the
query and the scope attached. It is not the same as "I did not look", and the
reader must be able to tell which one they received.

### Do not substitute a summary for the artifact

If the deliverable is a document, a patch or a file, the artifact is the answer
and the prose is its abstract. Deliver the reference, keep the bytes intact, and
never let a summary stand in for something the reader is about to rely on.

---

## 3. Structure

Structure exists so the reader can **stop early and still be correct**.

### The required order

1. **Verdict** — one line. What happened, on which of the three tiers
   (`solved` / `best_effort` / `blocked_with_evidence`). If the reader stops here,
   they must not be misled.
2. **The answer** — what was asked for, in the shape it will be used.
3. **Evidence** — what was checked, how, and with what result; anchors included.
4. **Gaps** — omissions, uncertainties, and what would resolve them.
5. **Artifacts** — durable references (path or id, plus a digest where it matters).
6. **Next actions** — only if something is required from the reader.

Two rules that matter more than the list itself:

- **The verdict is never buried.** A reader who reads only the first line must
  get a correct impression, including when the news is bad.
- **Detail follows summary.** Never make the reader reconstruct the top-level
  state from the details. That is the single most expensive structural failure,
  because it forces a full read of every return.

### Why this order

It mirrors how the reader decides: *what is it* → *can I use it* → *do I believe
it* → *what is missing* → *where is it* → *what now*. A return that follows the
order of the work (chronological, tool-by-tool) instead of the order of the
decision makes the reader do the inversion themselves, every time.

### Machine-legibility is part of it

The front door parses the return. A stable section shape — same headings, one
verdict line, tiers named — means it can route and decide without an LLM pass
over the whole text. Free-form prose is a legitimate *addition*; it is a poor
*interface*.

---

## A template for the return

```
VERDICT:  solved | best_effort | blocked_with_evidence
SUMMARY:  <one or two sentences; if the reader stops here, this is still true>

ANSWER
  <the thing that was asked for>

EVIDENCE
  - <check> → <result>  [anchor: path:line | command | url]
  - ...

GAPS
  - <what is not covered, with count and a resolvable reference>
  - <uncertainty, with what would resolve it>

ARTIFACTS
  - <path or id>  sha256:<digest if it will be re-verified>

NEXT
  - <only if the reader must act>
```

---

## A short critique checklist for a return

Read it as the consumer would, in this order:

1. **Can I state the outcome after one line?** If not, the verdict is buried.
2. **Can I falsify the main claim?** If not, it is an assertion.
3. **Is anything missing that I would not have noticed?** If you cannot tell, the
   omissions are not named.
4. **Could I route the artifact without retyping anything?** If not, it is not
   really delivered.
5. **Does any claim outrun its evidence?** Look for the sentence with no anchor
   that the whole decision rests on. There is usually exactly one.

The last is the most valuable check in this file. Returning work that is
**well-evidenced and slightly incomplete** is far better than work that is
**complete and quietly unsupported** — because the first can be used, and only the
second can be used *wrongly*.

# Work Order — fill this in and send it

Keep every heading. Delete the guidance lines once you are done.
An empty field is honest. A field filled with a guess is worse than empty —
the executor will treat your guess as a fact and build on it.

---

## Objective

<!-- One or two sentences. The OUTCOME, not the steps. What must be true
     when this is finished? Do not write "first do X, then Y" unless the
     order genuinely matters for correctness or safety. -->

## Definition of done

<!-- How someone else proves it. Name the CHECK, not the intention.
     Good: "`pytest tests/enrichment.py -q` exits 0"
     Good: "report.md answers the three questions in section 2, one dated
            source per claim, and lists `not found` where nothing was found"
     Good: "the artifact exists at path P and opens in the browser"
     Bad:  "the code is clean", "it works", "quality is high"
     If you cannot name a check, say so explicitly and ask the executor to
     propose one — that is a legitimate order, and a different kind of one. -->

## Not in scope

<!-- What must NOT change or be touched, and what is deliberately deferred.
     This protects you: without it, a capable executor may improve things you
     did not ask about, and you will pay to review those changes. -->

## Inputs and sources of truth

<!-- Exact paths, ids, URLs. Label each one:
       [AUTHORITATIVE] — the executor must trust this and nothing else
       [REFERENCE]     — context, may be stale
       [UNVERIFIED]    — someone claims this; check before relying on it
     Say what you withheld and why. If two sources disagree, say which wins. -->

## Authority

<!-- What the executor MAY change: files, repos, systems, accounts.
     What is READ-ONLY.
     Who approves spending money, writing to external services, publishing,
     or contacting third parties. Being explicit here prevents both
     overreach and a stalled task waiting for permission you already gave. -->

## Constraints

<!-- Deadline (and whether it is hard). Budget. Environment. Tool limits.
     The LANGUAGE and format of the deliverable. Anything that must be
     preserved (compatibility, existing behaviour, style). -->

## The decision this serves

<!-- What YOU will do with the result. One sentence.
     This is the highest-leverage field in the order: it lets the executor
     resolve ambiguity the way you would, instead of guessing or asking. -->

## Cost of error

<!-- What happens if this is wrong, or late. This is what sets how much
     verification and review the work deserves. -->

## Verification owner

<!-- Who runs the check, with what, and what a pass buys.
     "You run the check and record a receipt."
     "I will run it — give me the exact command."
     "Neither of us can fully verify this: give me the best proxy and say what
      it misses."
     Naming this up front prevents the closing argument where a result says
     "done" and nobody can tell whether that is true. -->

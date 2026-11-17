<!--
OUTLINE. Everything inside <!- - - -> comments is an instruction to you, and GitHub won't show it.
Write every sentence yourself, replacing each comment as you go. The fenced code blocks are your
own proof from this term: keep them, and put your own sentence above each one that says what it
proves. Target 500–1,000 words. Facts and dates: drafts/assignment6-evidence.md.

Title: add a line ABOVE this comment, "# ...", that says what you did. It should name both
the fix and the tool, e.g. the deepeval bug + the scout that found it.
-->

## Overview

<!--
One short paragraph, 4–6 sentences, covering:
- What deepeval is (an open-source Python library for evaluating LLM output), in one clause.
- The problem: when the judge model returns nothing, four conversation metrics give a perfect
  1.0, and three of them say "no retrieval contexts" even when there were some.
- What you did: you found the issue with your own scout, reproduced it, and opened PR #3634.
  The PR fixes the misleading explanations and changes no scores.
- Your tool part: the scout's search strategy and rubric are yours. Name them.
- How it stands today: PR open, not merged; the issue's opener reviewed it; no maintainer review yet.
- Link the PR: https://github.com/confident-ai/deepeval/pull/3634
-->

## What happened

<!--
Tell it in time order. Subheadings help. Rubric points, 10 each: real moments in order,
2+ proofs on the page, proof from real work, a change you made, a result that landed.
The contribution AND the tool each need their own proof.
-->

### Finding the issue

<!--
- Oct 18: scan 1 rejected all 5 (4 had open PRs your ranking couldn't see). You added
  -linked:pr / --no-assignee, and scan 2 accepted 3 of 5.
- Scan 2 also accepted docling#1705, which a commenter said no longer reproduces.
  This is your TOOL CHANGE moment: say what happened, then show the rule before and after (below).
- Say what the re-run proved (8/8 gold accepts, docling#1705 now rejects).
-->

Before (Unit 1 rubric, `bounded-scope`):
```
(d) fewer than 2 closed-unmerged PRs in its history when the issue is more than 1 year old.
```
After (2026-10-18):
```
(d) fewer than 2 closed-unmerged PRs in its history when the issue is more than 1 year old;
(e) no comment from the last 90 days reports that the problem no longer reproduces on the current
default branch or latest release, unless a later comment shows it still does.
```

<!--
- Nov 11: your earlier accepts had gone stale, so you ran scan 3: 59 results, 11 after ranking,
  1 accept (deepeval#3629).
- TOOL RESULT THAT LANDED: in that same scan, clause (e) rejected two already-fixed issues
  (docling#1705, docling#4147). Say this in your words.
-->

### Reading the code before claiming

<!--
- The issue implied changing the 1.0 score. You found a test that keeps 1.0 on purpose.
- CONTRIBUTION CHANGE moment: your plan changed from "change the score" to "fix the explanation".
  Say why the test changed your mind. Quote the two lines below.
- You put two options in your claim comment and asked which one they wanted:
  https://github.com/confident-ai/deepeval/issues/3629#issuecomment-6812196654
-->

```python
    score, reason = result  # must be unpackable
    assert score == 1.0
```

### Reproducing it

<!--
- No API key: a stub judge that returns empty lists. One sentence on why that's realistic
  (small or local judge models).
- What the output shows: two retrieval contexts in the conversation, yet the reason says none.
-->

```
TurnContextualRecallMetric    substantive reply  score=1.0  window reason='There were no retrieval contexts in the given turns to evaluate the contextual recall.'
TurnFaithfulnessMetric        substantive reply  score=1.0  window reason='<no claims to verify>'
```

<!-- Optional small moment: the first run printed None for every reason, because they only live in verbose_logs. -->

### The fix

<!--
- What changed: the caller passes the context count, and a new helper picks the right message.
  Faithfulness gets an honest explanation. Scores unchanged.
- Say what you deliberately did NOT change, and why (the tested 1.0; option 2 left for later).
-->

```diff
+    def _empty_verdicts_reason(self, retrieval_context_count: int) -> str:
+        if retrieval_context_count == 0:
+            return "There were no retrieval contexts in the given turns to evaluate the contextual recall."
+        return (
+            f"The evaluation model returned no verdicts for the "
+            f"{retrieval_context_count} retrieval context(s) in the given "
+            f"turns, so the contextual recall could not be evaluated."
+        )
 ...
         if len(verdicts) == 0:
-            return (
-                1,
-                "There were no retrieval contexts in the given turns to evaluate the contextual recall.",
-            )
+            return 1, self._empty_verdicts_reason(retrieval_context_count)
```

### Proving it

<!--
- 14 new tests. Without the fix, 11 fail. The 3 that pass are backward-compatibility checks,
  which is expected. Say this in your words.
- The 20 existing turn-metric tests that run pass before and after.
- The same repro script, after the fix (below).
-->

```
TurnContextualRecallMetric    substantive reply  score=1.0  window reason='The evaluation model returned no verdicts for the 2 retrieval context(s) in the given turns, so the contextual recall could not be evaluated.'
```

### What happened next

<!--
- RESULT THAT LANDED: the issue's opener (kittimzhe, a contributor, NOT a maintainer) confirmed your
  analysis on the issue and reviewed the PR. Quote ONE short line from them, attributed.
- Small second change: pr-title-check failed ("No release type found in pull request title"),
  so you added "fix:" and it passed.
- Be honest about CI: three failing checks are unrelated (one test, lint on untouched files, Vercel).
-->

## Limitations and what's next

<!--
Pick 2–3 real limits, each followed by the next step it leads to:
- Faithfulness still scores 1.0 whether there was nothing to check or extraction failed. The
  explanation admits it, but the score can't tell. Next: option 2, an opt-in score for empty
  extraction, once this lands.
- Reviewer suggestions not done yet: a stable, searchable marker in the message; dropping the
  unneeded f-prefixes. Next: push those to the PR.
- Your claim said you'd remove the unreachable return 0, and you didn't. Own it.
- Scout: no memory of past rejects (2 of 5 grading slots wasted in scan 3), and accepts go
  stale within about 3 weeks. Next: a skip-list of rejected URLs, and re-grade right before claiming.
- PR status: open, waiting on a maintainer.
-->

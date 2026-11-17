# Making Empty AI Evaluations Visible: A deepeval Fix and the Scout That Found It

## Overview

deepeval is an open-source Python library for evaluating language-model output. I investigated a bug in four conversation metrics: when the judge model returned no results, each metric still gave a perfect 1.0, and three incorrectly said there were no retrieval contexts even when contexts existed. I found the issue with an issue-scout tool whose GitHub search strategy and grading rules I built and refined during AI301. After reproducing the behavior, I opened [PR #3634](https://github.com/confident-ai/deepeval/pull/3634) to make the explanations accurate without changing scores. As of November 17, 2026, the PR is open and unmerged; the issue's author has reviewed it, and a newer review has identified a remaining edge case.

## What happened

### Finding the issue

I started by improving the tool I would use to find a contribution. On October 18, my first scout scan rejected all five graded issues; four already had open pull requests that my ranking missed. I added `-linked:pr` and `--no-assignee` to the searches. The second scan accepted three of five, but one accepted issue, `docling#1705`, had a recent comment saying its bug no longer reproduced. That was a mistake in my grading rule, not evidence that the issue was suitable.

The `bounded-scope` rule originally checked only this historical PR condition:

```
(d) fewer than 2 closed-unmerged PRs in its history when the issue is more than 1 year old.
```

I added a freshness check so a recent report of an already-fixed bug could disqualify an issue:

```
(d) fewer than 2 closed-unmerged PRs in its history when the issue is more than 1 year old;
(e) no comment from the last 90 days reports that the problem no longer reproduces on the current
default branch or latest release, unless a later comment shows it still does.
```

The changed rule rejected `docling#1705` while the eight known-good acceptance examples still passed eight out of eight. On November 11, earlier picks had gone stale, so I ran scan 3: 59 search results narrowed to 11 by ranking, with one of the five graded issues accepted, `deepeval#3629`. In that scan, the new clause also rejected two already-fixed issues, `docling#1705` and `docling#4147`. This was a concrete improvement to my tool, not just a rewritten rule.

### Reading the code before claiming

Issue [#3629](https://github.com/confident-ai/deepeval/issues/3629) sounded like a scoring bug: an empty answer from the AI judge still produced 1.0. My first instinct was to lower that score. But tracing the code and reading the existing faithfulness test changed my plan. The test explicitly required an empty-verdict case to keep the perfect score:

```python
    score, reason = result  # must be unpackable
    assert score == 1.0
```

Changing the default would break established behavior for conversations with nothing factual to verify. The more defensible first contribution was to fix the misleading *reason*, not the score. In [my claim comment](https://github.com/confident-ai/deepeval/issues/3629#issuecomment-6812196654), I offered that narrow approach and a second option with configurable scoring, then asked which was preferred. The issue's author favored the narrow approach.

### Reproducing it

I wrote a small reproduction using a stub judge that returned valid responses containing empty lists. That needed no API key and modeled a plausible failure from a smaller or local model: it can return the right output shape without producing useful findings. The conversation contained two retrieval contexts, yet the output said there were none. The faithfulness reason likewise made failed extraction look like a harmless absence of claims:

```
TurnContextualRecallMetric    substantive reply  score=1.0  window reason='There were no retrieval contexts in the given turns to evaluate the contextual recall.'
TurnFaithfulnessMetric        substantive reply  score=1.0  window reason='<no claims to verify>'
```

I also learned that these per-window reasons were in `verbose_logs`, rather than the top-level reason field I initially inspected.

### The fix

For contextual precision, recall, and relevancy, I passed the actual retrieval-context count from the caller and added a helper to select the correct message. Zero contexts still produce the old wording; contexts with zero judge verdicts now report that the judge returned none. For faithfulness, I replaced the ambiguous message with one that says no claims were extracted and acknowledges that extraction may have failed. This excerpt from the recall change shows the distinction:

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

I deliberately kept every score unchanged, including the tested 1.0 behavior, and left optional scoring policy for a future PR. I also left an unreachable `return 0` in precision untouched, although I had originally said I would remove it.

### Proving it

I added 14 tests covering the contextual metrics and faithfulness, synchronous and asynchronous paths, and compatibility for old callers. Without the fix, 11 failed; the three passing tests were expected backward-compatibility checks. The 20 existing runnable turn-metric tests passed both before and after the change. With the patch, the same reproduction now said what actually happened while keeping 1.0:

```
TurnContextualRecallMetric    substantive reply  score=1.0  window reason='The evaluation model returned no verdicts for the 2 retrieval context(s) in the given turns, so the contextual recall could not be evaluated.'
```

The combined targeted run reported 34 passed and 35 skipped; the skipped tests required an API key. I documented the reproduction and results in the PR rather than relying on a claim that the fix worked.

### What happened next

I opened PR #3634 on November 12. Its first title failed the `pr-title-check` because it lacked a release-type prefix; I changed it to start with `fix:`, and that check passed. The issue's author, **kittimzhe**, reviewed the PR and wrote that it “matches option 1 cleanly.” They are the reporting contributor, not a project maintainer. Their review also identified unrelated failing CI checks: a core test, formatting in files I did not edit, and a Vercel deployment authorization requirement.

On November 17, another reviewer found an uncovered faithfulness case: claim extraction can succeed while verdict generation returns nothing. In that situation, my new text incorrectly says no claims were extracted. The reviewer supplied a suggested patch and tests. That feedback matters: the PR is a reviewed proposal, not a merged or fully finished fix.

## Limitations and what's next

The biggest immediate task is to handle that newly reported faithfulness case, distinguish the stages accurately, and run the suggested tests before updating PR #3634. I also need to consider a stable phrase that reporting tools can search for, remove unnecessary `f` prefixes, and address the unreachable `return 0` I left behind. A possible later contribution is an opt-in alternative score for empty extraction; the current patch makes failures more visible but cannot distinguish them numerically from genuine nothing-to-check cases.

My scout needs work too. It has no memory of rejected URLs, so scan 3 spent two of its five grading slots on previously rejected issues, and accepted picks can become stale within weeks. I would add a rejection skip-list and re-grade immediately before claiming. Until the revised fix is reviewed and merged, I will describe this work as a pull request I opened, not a fix that has landed in deepeval.

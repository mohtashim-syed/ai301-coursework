# Contribution: Conversational turn metrics score empty extraction as 1.0 (deepeval#3629)

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** [1]
**Student:** Mohtashim Syed
**GitHub Username:** mohtashim-syed
**Issue:** https://github.com/confident-ai/deepeval/issues/3629
**Pull Request:** [GitHub PR link, once opened]

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: issue-scout. Two scans of RAG/document-parsing repos, one strategy fix, one rubric patch (2026-10-18)**

I installed the scout over my Unit 1 skill and wrote five `gh search issues` queries across 17 Python RAG/LLM and document-parsing repos, ranked by search fields only (no assignee, updated within 180 days, bugs first, fewest comments). Scan 1 rejected all five candidates because four already had open PRs that my ranking couldn't see, so I added `--no-assignee` and `-linked:pr` to every query. My `doc` label filter had also matched `docling-document` by substring and silently dropped every docling issue, so I changed it to match whole label names, and with both fixes scan 2 accepted 3 of 5. One of those accepts, docling#1705, had a comment saying it no longer reproduces on main, so I added an "already fixed" clause to my rubric's `bounded-scope` check; the Unit 1 harness on the 8 gold-accept bundles still agreed 8/8, and docling#1705 now rejects.

**Unit 6: [one line on the tool work]**

[2-4 sentences: the voice-guard, the repo-memory file. What broke or
fell short, and what changed because of it. "Nothing broke" counts when
it is true - say how you know.]

**Unit 7: [one line on the tool work]**

[2-4 sentences: the ship-gate and your gate manifest. What it produced
on your real branch, what fought you, what changed because of it.]

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

---

## Why I Chose This Issue

[1-2 paragraphs in your own words: how you searched, what your scan
surfaced, what your scout's verdict said, why this issue fits your
profile, and what you hope to learn. If your scout's verdict on it
was reject and you chose it anyway, say so here and say why; choosing
against your own tool silently is what loses points. Close with an
honest read of the tool itself: where the scout fell short on a real
wild search and what you changed or would change, or what convinced
you it held up. **Record the link to your claim comment in this section too.**
Not claimed yet? Write `claim pending` and today's date where the link goes,
and add the link the moment your claim lands.]

**Toolkit**

- Tool: issue-scout (your scout: your search strategy, your rubric),
  and how you used it
- What it produced: [the scan and its verdict on this issue, described]
- Difficulties: [where it fell short, or "none met" if none did]
- What changed: [what you patched or would update, or "nothing"]

---

## Understanding the Issue

**Problem Description**

[In your own words, what is broken or missing?]

**Expected Behavior**

[What should happen?]

**Current Behavior**

[What actually happens?]

**Affected Components**

[Which parts of the codebase are involved?]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Reproduction Process

**Environment Setup**

- macOS (Darwin 25.5, Apple Silicon), Python 3.14.6 (Homebrew), pip.
- deepeval cloned at `main` commit `debe795`, installed into a virtual environment with `python3 -m venv .venv && .venv/bin/pip install -e . pytest pytest-asyncio` (installs as deepeval 4.3.4).
- No API key is needed: the reproduction uses a stub judge, a `DeepEvalBaseLLM` subclass whose `generate()` returns valid schema instances with empty lists, the way a small local judge model does when extraction quietly fails.
- Challenges:
  - Python 3.14 prints `SyntaxWarning: 'return' in a 'finally' block` from deepeval's `loop.py` on every import. It's harmless, so I ran with `-W ignore`.
  - The per-window score and reason aren't stored on the metric. They only appear in `metric.verbose_logs`, so the script reads the `Reason:` line from there.

**Steps to Reproduce**

1. In the deepeval checkout, on branch `repro/3629-empty-extraction`, run `.venv/bin/python repro/issue_3629.py`.
2. The script runs each of the four turn metrics twice: once on a conversation with a substantive three-sentence HTTPS answer, and once on "Sure, happy to help!". Both conversations have two retrieval contexts.
3. Observed result: every metric scores 1.0 on both conversations. Faithfulness's reason is `<no claims to verify>` both times. The three contextual metrics say "There were no retrieval contexts in the given turns", even though there were two.

**Reproduction Evidence**

- **Commit showing reproduction:** https://github.com/mohtashim-syed/deepeval/commit/15037a51eda223f502dfe83e149ebbc8a08eb9b9
- **Screenshots/logs:** [`repro/issue_3629_output.txt`](https://github.com/mohtashim-syed/deepeval/blob/repro/3629-empty-extraction/repro/issue_3629_output.txt) in the same commit:
  ```
  TurnFaithfulnessMetric           substantive reply  score=1.0  window reason='<no claims to verify>'
  TurnContextualRecallMetric       substantive reply  score=1.0  window reason='There were no retrieval contexts in the given turns to evaluate the contextual recall.'
  ```
- **My findings:**
  - The bug reproduces as reported: a failed extraction scores the same 1.0 as a reply with nothing to check.
  - The issue describes all four metrics as the same bug, but the code shows two different cases:
    - **Faithfulness:** 1.0 for an empty claim list is a deliberate choice. `test_turn_faithfulness_metric_empty_verdicts.py` asserts it for replies like "Sure, happy to help!".
    - **The three contextual metrics:** the real defect is a wrong reason. The text says there were no retrieval contexts when the judge actually returned nothing for contexts that exist.
  - I posted both points in my claim comment before writing any code.

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Solution Approach

**Analysis**

**Contextual metrics.** In `deepeval/metrics/turn_contextual_{precision,recall,relevancy}/`:

- `_get_<metric>_scores` collects the window's `retrieval_context` and calls `_generate_verdicts`.
- `_generate_verdicts` returns `[]` early when there is no context. Otherwise it returns whatever list the judge produced, which can also be `[]`.
- `_get_interaction_score_and_reason` then only receives `verdicts`. Its `if len(verdicts) == 0` branch can't tell "no contexts" from "judge returned nothing", so it always returns 1 with the "no retrieval contexts" message.

The information that would separate the two cases (`len(retrieval_context)`) exists in the caller but is never passed down.

**Faithfulness.** In `turn_faithfulness.py:437/451`, an empty claim list scores 1.0 with `<no claims to verify>`. A failed extraction and a reply with no claims both produce an empty list, so no code can tell them apart without asking the judge again.

**Proposed Solution**

This is option 1 from my claim comment: make the failure visible without changing any default score.

- The contextual metrics pass the context count down, and the reason says the judge returned no verdicts for N contexts.
- Faithfulness gets a reason that says no claims were extracted and that extraction may have failed.

Deliberately not changed:
- **Scores.** The faithfulness 1.0 is tested on purpose.
- **The early `return 0`** in `TurnContextualPrecision._calculate_interaction_score`. It can't be reached from `measure`, and it returns the same value as falling through.
- **`role_adherence`**, from the opener's second comment, which is a separate case.
- **A configurable empty score** (option 2). It's left as a follow-up if the maintainers want it.

**Implementation Plan**

1. In each contextual metric, add `retrieval_context_count: int = 0` to the sync and async `_get_interaction_score_and_reason`, and pass `len(retrieval_context)` from both per-window callers.
2. Add `_empty_verdicts_reason(count)`: it keeps the old message for 0 and names the count otherwise. The default of 0 keeps old callers' behavior.
3. In `turn_faithfulness.py`, replace `<no claims to verify>` with a module constant `_NO_CLAIMS_REASON`.
4. Add `tests/test_metrics/test_turn_metrics_empty_extraction.py` with a stub judge.

Deviations:
- My first formatting pass used the newest black. It reflowed an unrelated `textwrap.dedent` block in `turn_contextual_relevancy.py`, so I reverted, installed black 25.1.0 (the version `pyproject.toml` pins) and re-applied. The diff now only touches the fix.
- I kept the dead `return 0`, although my claim comment listed removing it. Removing it changes nothing a user can see, and it would add noise to the review.

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Testing Strategy

1. **Reproduction, before and after.** I ran the same `repro/issue_3629.py` against `main` and against the fix branch. Scores stay 1.0 in both runs, as intended. The window reason changes:
   - Before: "There were no retrieval contexts in the given turns to evaluate the contextual recall."
   - After: "The evaluation model returned no verdicts for the 2 retrieval context(s) in the given turns, so the contextual recall could not be evaluated."
   - Precision and relevancy change the same way. Faithfulness changes from `<no claims to verify>` to "No claims were extracted from the assistant turns…".
2. **New tests fail without the fix.** `tests/test_metrics/test_turn_metrics_empty_extraction.py` has 14 tests:
   - each contextual metric, sync and async, through `measure()`;
   - the no-context message is unchanged;
   - callers that don't pass the count keep the old message;
   - faithfulness, sync and async.

   With the metric changes stashed, 11 fail. The 3 that still pass are the backward-compatibility checks, which should pass either way. With the fix, all 14 pass.
3. **Existing suite.** `pytest tests/test_metrics/test_turn_*.py tests/test_metrics/turn_contextual_relevancy_metric.py` gives 34 passed and 35 skipped with the fix. The 20 pre-existing tests that run pass on both `main` and the fix, including the four in `test_turn_faithfulness_metric_empty_verdicts.py` that assert the 1.0 score. The 35 skipped tests need `OPENAI_API_KEY`, and they skip the same way on `main`, so they don't cover this change.

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Implementation Notes

**2026-11-11**

- **Re-checked my scan-2 accepts before choosing; both were gone.**
  - markitdown#2288 had an open PR (#2721).
  - docling#3170 had been traced to docling-parse, which is C++.
- **Ran scan 3.** It returned 59 results and 11 after ranking. deepeval#3629 was the only accept.
- **Read the code before claiming.**
  - Found the existing test that locks faithfulness at 1.0.
  - Found that the contextual metrics' real defect is a wrong reason, not the score.
  - Wrote both into the claim comment instead of promising a score change ([claim](https://github.com/confident-ai/deepeval/issues/3629#issuecomment-6812196654)).
- **Reproduced with a stub judge.**
  - First attempt: the window reasons came back `None`, because I'd looked for them on the metric object.
  - They only exist in `verbose_logs`, so I changed the script to read them from there.

**2026-11-11 (later)**

- **No maintainer reply yet, so I went ahead with option 1.** It changes no scores, so it's the low-risk choice either way.
- **Implemented the fix:** commit `a311ce2` on `fix/3629-empty-verdict-reasons`.
- **Wrong turn:** the newest black reformatted an unrelated block. I reverted and used the pinned 25.1.0.
- **Kept the unreachable `return 0`** to keep the diff focused, although my claim mentioned removing it.
- **Tested:** 11/14 of the new tests fail without the fix, and the 20 existing turn-metric tests that run pass with and without it.

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Pull Request

**PR Link:** [GitHub PR URL when opened]

**PR Description:** [what the PR says; much of the content above
adapts into it]

**Maintainer Feedback:**

- [Date]: [feedback received, or "none yet"; silence is a normal
  state and costs you nothing]
- [Date]: [how you addressed it]

**Status:** [Awaiting review / Iterating / Merged / Closed. The
status is a fact you record, never a grade: the PR is graded as
opened.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Learnings and Reflections

**Technical Skills Gained**

[What you learned technically, named concretely.]

**Challenges Overcome**

[What was hard and how you solved it; point at the real events, the
Toolkit blocks above are full of them.]

**What I'd Do Differently Next Time**

[Reflection on your process.]

---

## Resources Used

- [deepeval#3629](https://github.com/confident-ai/deepeval/issues/3629): the opener's file:line chain (`turn_faithfulness.py:358,382,437`) was the map I checked the code against.
- [`tests/test_metrics/test_turn_faithfulness_metric_empty_verdicts.py`](https://github.com/confident-ai/deepeval/blob/main/tests/test_metrics/test_turn_faithfulness_metric_empty_verdicts.py): showed that the 1.0 score is deliberate, and gave me the stub-judge pattern (a `DeepEvalBaseLLM` with no provider) that my reproduction and tests copy.
- deepeval's `pyproject.toml` (`black = "^25.1.0"`, `line-length = 80`): the formatter version that kept my diff free of unrelated changes.

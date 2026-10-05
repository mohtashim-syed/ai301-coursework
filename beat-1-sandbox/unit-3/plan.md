# Plan: issue #59, faithfulness checker scores claims unsupported when the context uses different words

Issue: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/59
My repro: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/59#issuecomment-5987554411

## Repro evidence this plan builds on

From my posted repro (commit `2f4e82f`, macOS 26.5.1 arm64, Python 3.14.6, pytest 9.1.1):

```
$ .venv/bin/pytest tests/unit/test_faithfulness_checker.py -q --runxfail -k test_multiple_context_chunks
>       assert score > 0.5
E       assert 0.0 > 0.5
2026-10-04 22:19:07 [info     ] faithfulness_checked           claims_count=1 score=0.0 supported_count=0
```

```
issue example: 0.0      # "Knows Python." vs "python expert"
control:       1.0      # "Knows Python." vs "The candidate knows Python"
token overlap: ['and', 'docker']
```

The token overlap is for the test's claim "The developer has Python, JavaScript, and Docker experience" against its three context chunks.

## Diagnosis

`_is_supported()` in `rag/evaluator/faithfulness_checker.py` marks a claim as supported only if it shares at least two non-stop-word tokens with the context, using `set(text.lower().split())`. The repro shows two things working against that:

1. **Punctuation stays on tokens.** `python,` and `javascript,` never match `python` or `javascript`, so the test's claim keeps only `docker` once `and` is removed as a stop word. One meaningful token is below the threshold of two, so the score is 0.0.
2. **The threshold counts shared wording, not the skill the claim is about.** "Knows Python" vs "python expert" shares only `python`, so it scores 0.0. The control, which repeats the claim's exact words, scores 1.0. The difference between the two runs is filler phrasing ("knows", "the candidate"), not the skill.

The same file has two more strict xfails tagged #59, which I read but haven't run separately: `test_partial_support_returns_middle_score` (one sentence, half supported, expects 0.2 < score < 0.8) and `test_multiple_claims_varying_support` (three short claims, two supported, expects 0.2 < score < 0.8). The second also depends on `_extract_claims()` keeping "Knows Rust", which at exactly 10 characters is dropped by the current `len(s) > 10` filter. With the current all-or-nothing per-claim scoring, the first can never land between 0 and 1, because it has only one claim.

## Scope

In scope, one change to how `FaithfulnessChecker` matches a claim against the context, all in `rag/evaluator/faithfulness_checker.py`:
- Tokenize with a regex over lowercase text (`[a-z0-9+#]+`) so punctuation no longer sticks to words.
- Move the existing stop-word list, unchanged, to a module constant so the new helpers can share it. Add a small `FILLER_WORDS` set of generic resume wording (`developer`, `candidate`, `has`, `shows`, `knows`, `experience`, `expertise`, `expert`, `skills`, `skilled`, `knowledge`, `strong`, ...), so that a claim is judged on the skill it names.
- Score each claim by the share of its content tokens found in the context, and make `check()` return the mean of those shares. `_is_supported()` stays (tests call it) and returns `True` when that share is at least 0.5.
- `_extract_claims()` keeps sentences of two or more words instead of more than 10 characters. Claims with no content tokens are skipped, and if none are left, `check()` returns the existing neutral 0.5.
- Remove the `xfail` markers from the three #59 tests in `tests/unit/test_faithfulness_checker.py`.

Not in scope:
- Issue #60 (`text: None` crashes the join). Its strict xfail must stay failing after my change.
- Embeddings or any semantic-similarity model.
- `rag/evaluator/eval_suite.py`, which calls `check()` and gets a 0.0–1.0 score as before.
- Any other evaluator.

## Files I'll touch

- `rag/evaluator/faithfulness_checker.py`: `check()`, `_extract_claims()`, `_is_supported()`, a new `_content_tokens()` and `_support_ratio()`, and the `STOP_WORDS` / `FILLER_WORDS` constants.
- `tests/unit/test_faithfulness_checker.py`: remove the three #59 `xfail` decorators. No test bodies change.

## Approach, in order

1. Branch `fix/59-faithfulness-wording` from `main` in my fork.
2. Add the tokenizer and word sets, then `_support_ratio()`, and rewrite `_is_supported()` on top of it.
3. Change `check()` to average the per-claim shares, and change the `_extract_claims()` filter.
4. Run the whole faithfulness test file before touching the tests, so the three #59 tests show `XPASS(strict)` and #60's still shows `xfailed`. Then remove the three markers.

## Test plan

Re-run my repro steps against the change:

1. `.venv/bin/pytest tests/unit/test_faithfulness_checker.py -q --runxfail -k test_multiple_context_chunks`. Before: `E assert 0.0 > 0.5`. After: `1 passed`, with `score=1.0` in the log line.
2. My step 3 script. Before: `issue example: 0.0`, `control: 1.0`. After: `issue example: 1.0`, and the control stays `1.0`.
3. `.venv/bin/pytest tests/unit/test_faithfulness_checker.py -q` with the markers removed. Expected `21 passed, 1 xfailed`: the three #59 tests pass and #60's `test_none_context_chunk_text` stays xfailed.
4. `.venv/bin/pytest tests/unit -q` before and after, to confirm no other unit test changes outcome. Before, which I ran on `main` at `2f4e82f` on the same machine as my repro (not in my posted repro comment): `375 passed, 53 xfailed, 2 warnings in 10.16s`. After, expected: `378 passed, 50 xfailed`, i.e. only the three #59 tests move from xfailed to passed.

## Risks and unknowns

- `FILLER_WORDS` is a judgment call. A word like `expert` or `strong` could matter in some claim, and the list will never be complete. I'm keeping it small and covering it only with the existing tests. That's a trade-off I'll flag for review, not a solved problem.
- `check()` returns a different number for the same input (a mean share instead of a count of passing claims). The only caller in the repo is `eval_suite.py`, and I haven't yet checked whether anything there depends on the old values beyond the 0.0–1.0 range.
- Changing the claim filter from characters to words will extract some fragments that were dropped before. I haven't measured that on real feedback text.

## Deviations

The approach didn't change. The commits on `fix/59-faithfulness-wording` make exactly the edits listed under Scope and Files (plus the type fix in item 3), and every test-plan expectation came out as written: `1 passed` with `score=1.0`, issue example `1.0`, `21 passed, 1 xfailed` for the file, and `378 passed, 50 xfailed` for `tests/unit`. Four smaller differences from the plan as first drafted:

1. **Stop words stayed as they were.** My first draft added eleven stop words (`with`, `on`, `they`, ...). Before posting, I dropped them, because no test or repro step needed them, and moved the existing list to `STOP_WORDS` unchanged. The posted plan comment already reflects this.
2. **One risk is now resolved.** I had listed "haven't checked whether `eval_suite.py` depends on the old values" as an unknown. After the build I read `rag/evaluator/eval_suite.py`. It averages the faithfulness score with the relevance score (`overall = (relevance + faithfulness) / 2`) and applies no threshold, and nothing else in the repo calls `check()`. So the new values change `overall_score` numerically but don't break any contract. The filler-word list and the claim-filter change remain open risks, as stated above.
3. **A second commit for a type error.** Before opening the PR I ran the repo's own checks (`make lint`, `black --check .`, `make typecheck`, `make test-unit`). My test plan had only named pytest. `make typecheck` reported three mypy errors in `check()`, because `ratios` was reassigned from `list[float | None]` to the filtered list, so mypy never narrowed the type. Follow-up commit `44024e7` (`fix(rag): keep faithfulness ratio list typed as float for mypy (#59)`) filters into a new name (`claim_ratios` → `ratios`). Behavior is unchanged: the same `378 passed, 50 xfailed`. `make typecheck` now reports `Success: no issues found in 76 source files`, the same as `main`. My test plan should have included the repo's full check list from the start.
4. **The full unit run shows `1 warning` instead of the `2 warnings` on `main`.** I didn't change any warning-related code, and I haven't looked into which warning went away. I'm noting it rather than explaining it.

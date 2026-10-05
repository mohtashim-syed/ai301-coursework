# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

mohtashim-syed

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/59#issuecomment-5987861779

Plan for #59, built on my repro above (`assert 0.0 > 0.5` in `test_multiple_context_chunks`, and `"Knows Python."` vs `"python expert"` scoring 0.0 while the control scores 1.0). I worked this out from my own run. It ends up close to Znasif's plan above. What mine adds is a before/after for the whole unit suite, and it flags the one caller of `check()` (`rag/evaluator/eval_suite.py:45`) as still to check.

**Diagnosis.** `_is_supported()` splits on whitespace, so `python,` and `javascript,` never match `python` and `javascript`. In my run, the only shared tokens for the test's claim were `['and', 'docker']`, which leaves one meaningful token against a threshold of two. The issue's own example fails for a different reason: "Knows Python" vs "python expert" share only `python`, so the claim fails on wording ("knows", "the candidate") rather than on the skill it names.

**Change (all in `rag/evaluator/faithfulness_checker.py`):**
- Tokenize with a regex so punctuation doesn't stick to words.
- Drop the existing stop words plus a small, named set of generic resume words (`developer`, `knows`, `experience`, `skills`, ...).
- Score each claim by the share of its remaining words found in the context, and have `check()` return the mean. From reading the other two #59 tests (I haven't run them on their own yet), this is needed for `test_partial_support_returns_middle_score`: it has a single claim, so all-or-nothing scoring can only return 0.0 or 1.0, never the 0.2–0.8 it expects.
- `_is_supported()` keeps its signature and returns `True` at a share of 0.5 or more.
- `_extract_claims()` keeps sentences of 2+ words instead of more than 10 characters. "Knows Rust" is exactly 10 characters, so by my reading `test_multiple_claims_varying_support` loses it today.

Then I'll remove the `xfail` markers from the three tests tagged #59.

**Not changing:** #60's `text: None` crash (its strict xfail should still fail), any embedding or synonym matching, or `eval_suite.py`, which still gets a 0–1 score.

**Test plan.**
- My repro command should go from `E assert 0.0 > 0.5` to `1 passed`, and the issue example from `0.0` to `1.0`.
- The faithfulness test file should end at `21 passed, 1 xfailed`.
- `pytest tests/unit -q` on `main` gave me `375 passed, 53 xfailed, 2 warnings in 10.16s` (a run on my machine, not in my repro comment). After the change I expect `378 passed, 50 xfailed`, with only the three #59 tests moving.

**Unknowns.** The generic-word list is a judgment call and won't be complete. I'll keep it short and flag it for review. `check()` also returns different numbers for the same input now, and I haven't yet checked whether anything in `eval_suite.py` depends on the old values.


---

## Your branch

**Branch**

`fix/59-faithfulness-wording` (in https://github.com/mohtashim-syed/pathreview-ai301-fa26-s3)

**Evidence**

My Unit 2 repro steps from https://github.com/codepath/pathreview-ai301-fa26-s3/issues/59#issuecomment-5987554411, run on macOS 26.5.1 (arm64), Python 3.14.6, pytest 9.1.1. "Before" is `main` at `2f4e82f` (the output I posted in Unit 2, plus the full-suite run I did before building). "After" is branch `fix/59-faithfulness-wording` at commit `2ef6c02`.

**Before** (`main`, `2f4e82f`):

```
$ .venv/bin/pytest tests/unit/test_faithfulness_checker.py -q
..x...x......x....x...                                                   [100%]
18 passed, 4 xfailed in 0.66s

$ .venv/bin/pytest tests/unit/test_faithfulness_checker.py -q --runxfail -k test_multiple_context_chunks
>       assert score > 0.5
E       assert 0.0 > 0.5

tests/unit/test_faithfulness_checker.py:106: AssertionError
----------------------------- Captured stdout call -----------------------------
2026-10-04 22:19:07 [info     ] faithfulness_checked           claims_count=1 score=0.0 supported_count=0
FAILED tests/unit/test_faithfulness_checker.py::TestFaithfulnessChecker::test_multiple_context_chunks
1 failed, 21 deselected in 0.10s

$ .venv/bin/python -c '...step 3 script from the repro comment...'
2026-10-04 22:22:24 [info     ] faithfulness_checked           claims_count=1 score=0.0 supported_count=0
issue example: 0.0
2026-10-04 22:22:24 [info     ] faithfulness_checked           claims_count=1 score=1.0 supported_count=1
control:       1.0
token overlap: ['and', 'docker']

$ .venv/bin/pytest tests/unit -q
375 passed, 53 xfailed, 2 warnings in 10.16s
```

**After** (`fix/59-faithfulness-wording`, `2ef6c02`):

```
$ .venv/bin/pytest tests/unit/test_faithfulness_checker.py -q
21 passed, 1 xfailed in 0.08s

$ .venv/bin/pytest tests/unit/test_faithfulness_checker.py -q --runxfail -k test_multiple_context_chunks -rA
2026-10-04 22:56:27 [info     ] faithfulness_checked           claims_count=1 score=1.0 supported_count=1
1 passed, 21 deselected in 0.09s

$ .venv/bin/python -c '...same step 3 script...'
2026-10-04 22:56:27 [info     ] faithfulness_checked           claims_count=1 score=1.0 supported_count=1
issue example: 1.0
2026-10-04 22:56:27 [info     ] faithfulness_checked           claims_count=1 score=1.0 supported_count=1
control:       1.0
token overlap: ['and', 'docker']

$ .venv/bin/pytest tests/unit -q
378 passed, 50 xfailed, 1 warning in 4.65s
```

The issue example went from `0.0` to `1.0` and the control stayed at `1.0`. The repro test went from `assert 0.0 > 0.5` to passing with `score=1.0`. The file went from `18 passed, 4 xfailed` to `21 passed, 1 xfailed`: the three #59 tests pass, and #60's `test_none_context_chunk_text` is still xfailed. The whole unit suite moved by exactly those three tests. The `token overlap` line is unchanged because that part of the script splits on whitespace itself, as the old code did, to show what the old code compared; it doesn't call the checker.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Full run, v1 files: `agreement: 19/20 scored items  (bar: 18/20: PASS)`, `categories: clear-accept 6/7  scope-creep 4/4  thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4`. One disagreement: `pkg-14  clear-accept  accept  reject   NO     failed: executable, honest-unknowns`.
2. Partial run after loosening `executable`, `--only pkg-14,pkg-10,pkg-17,pkg-18`: `agreement: 4/4 scored items` (partial; not a bar run).
3. Full confirming run, saved with `--save-run eval-run.txt`: `agreement: 20/20 scored items  (bar: 18/20: PASS)`, `categories: clear-accept 7/7  scope-creep 4/4  thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4`.

**Package analysis**

`pkg-14` (zellij-org/zellij, OSC color-query responses leaking into the pane on reattach). Gold label: `accept` ("honestly scoped-down: reattach handshake fix with a regression-window repro; defers the untestable Windows variant and says so"). My rubric in run 1: `reject`, `failed: executable, honest-unknowns`. Run 3: `accept`.

Only `executable` was required; `honest-unknowns` is preferred and can't change the verdict. The grader's evidence for `executable` was: "Only crate-level areas named ('client attach/reattach path in zellij-server'); 'exact functions to be pinned in the PR after tracing', so no concrete file/function/site." My v1 pass condition required that "at least one concrete file, function, or code site is named", and the plan doesn't name a function. It says "consuming or draining pending OSC query responses in the client attach path in `zellij-server`'s client connection handling before pane input is wired", and leaves the exact functions to "be pinned in the PR after tracing the query issuance with debug logs, which I have working".

Read against what the check is for (could a stranger start?), the plan passes. It chooses one approach (drain the OSC responses before input is wired, bounded "to OSC response patterns rather than a time window"), names a specific code path inside a named crate, and has a working way to find the exact lines. That is different from the unbuildable packages: pkg-17's "gocui? tcell? not sure" doesn't choose a layer, and pkg-18's "recover() 'somewhere'" doesn't choose a place. My v1 wording treated "no function name yet" the same as "no decision yet", so the gold label is right. The fix was to accept a specific code path in a named module as a location, while still failing "somewhere", a whole subsystem, or an open choice of approach.

**Check rationale**

Check `executable` (required), as it reads now:

> | executable | The plan's change/approach: the files, functions, or code sites named and the approach chosen. | A stranger could start the work without asking the author anything: one approach is chosen, and the place to make it is named concretely enough to open, either a file/function or a specific code path inside a named module or package (e.g. "the reattach path in `zellij-server`'s client connection handling"), even if the exact function is left to pin during the build. Fails if the location is "somewhere" or only a whole layer/subsystem, the approach is a choice left open ("X or Y, whichever is easier", "not sure which layer"), or the plan is only "investigate/profile and then decide". | required |

Why it reads this way: the unbuildable family is about decisions deferred to build time, not missing line numbers. v1 required a named file or function and so rejected pkg-14, whose author had made every real decision (approach, code path, how to bound the drain) and only left the function name to find with working debug logs. I moved the test from "is a function named?" to two observable conditions: is one approach chosen, and is the location concrete enough to open? Each fail phrase is quoted from a package that should fail. "somewhere" is pkg-18 ("recover() 'somewhere'"). "X or Y, whichever is easier" is pkg-18's "upstream or vendored, whichever is easier". "not sure which layer" is pkg-17 ("gocui? tcell? not sure"). "investigate/profile and then decide" is pkg-10's profile-and-optimize plan. I rejected loosening it to "names an area of the code", because pkg-17 names areas (the input stack) and still can't be started. "Only a whole layer/subsystem" is there to block exactly that.

**Trade-offs**

The looser `executable` gives up a guarantee that the exact edit site is known before building. A plan can now pass with "the attach path in module X" and still find, while building, that the real fix lives somewhere else. I accept that miss. The rubric records it as a deviation instead of blocking the plan, and the `## Deviations` section exists for exactly that.

Because this loosened a check, I ran canaries before spending a full run. `--only pkg-14,pkg-10,pkg-17,pkg-18` covered the flipped package plus all three unbuildable packages, the category this change could leak into. pkg-10, pkg-17 and pkg-18 all stayed `reject` and pkg-14 flipped to `accept` (`agreement: 4/4 scored items`). The change can't reach the two small thread-convention packages (pkg-04, pkg-20), which are graded by `thread-and-convention`, not `executable`. The confirming full run held at 20/20 with every category matched, so nothing else moved.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.

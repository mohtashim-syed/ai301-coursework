# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`. This file is graded at the path above; a copy
kept anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your identity upstream

**GitHub username**

mohtashim-syed

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/59#issuecomment-5987473163

I'd like to investigate this one as my first contribution to Path Review.

The issue says `_is_supported()` in `rag/evaluator/faithfulness_checker.py` only counts a claim as supported when it shares two or more non-stop-words with the context, so "Knows Python" scores 0.0 against "python expert", and `test_multiple_context_chunks` fails with `assert 0.0 > 0.5`.

Next, I'll set up the repo from `docs/SETUP.md` on my own machine, run `pytest tests/unit/test_faithfulness_checker.py -q` against current `main`, and post a reproduction report here with my environment, the exact commands, and the output I see. I'll keep any guess about the cause separate from what I actually observe.


**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/59#issuecomment-5987554411

Reproduction report: reproduced on current `main`.

**Environment**
- Path Review at commit `2f4e82f52efbcfcc57d65b3fa5348672163ca088` (`main`, cloned from my fork)
- macOS 26.5.1 (arm64)
- Python 3.14.6 in a fresh `.venv`, pytest 9.1.1
- Set up with the venv and `pip install -e ".[dev]"` steps from `make setup` in `docs/SETUP.md`. I skipped Docker, migrations, seeding and the frontend because this is a unit test of `rag/evaluator/faithfulness_checker.py`, which doesn't touch the database or the API.

**Steps**
```bash
git clone https://github.com/mohtashim-syed/pathreview-ai301-fa26-s3.git
cd pathreview-ai301-fa26-s3
git checkout 2f4e82f52efbcfcc57d65b3fa5348672163ca088
python3 -m venv .venv
.venv/bin/python -m pip install --upgrade pip setuptools wheel
.venv/bin/pip install -e ".[dev]"

# 1. The test as it ships (marked xfail strict for #59)
.venv/bin/pytest tests/unit/test_faithfulness_checker.py -q

# 2. The same test with the xfail marker ignored
.venv/bin/pytest tests/unit/test_faithfulness_checker.py -q --runxfail -k test_multiple_context_chunks
```

**Observed**

Step 1: `18 passed, 4 xfailed in 0.66s`

Step 2:
```
>       assert score > 0.5
E       assert 0.0 > 0.5

tests/unit/test_faithfulness_checker.py:106: AssertionError
----------------------------- Captured stdout call -----------------------------
2026-10-04 22:19:07 [info     ] faithfulness_checked           claims_count=1 score=0.0 supported_count=0
FAILED tests/unit/test_faithfulness_checker.py::TestFaithfulnessChecker::test_multiple_context_chunks
```

Step 3: the issue's own example, the control it mentions, and the token overlap `_is_supported()` computes for the test's claim (`set(text.lower().split())` on each side):
```bash
.venv/bin/python -c '
from rag.evaluator.faithfulness_checker import FaithfulnessChecker
c = FaithfulnessChecker()
print("issue example:", c.check("Knows Python.", [{"text": "python expert"}]))
print("control:      ", c.check("Knows Python.", [{"text": "The candidate knows Python"}]))
claim = "The developer has Python, JavaScript, and Docker experience"
ctx = "Python expertise shown in backend projects. JavaScript skills demonstrated in frontend development. Docker and containerization knowledge evident in CI/CD pipelines."
print("token overlap:", sorted(set(claim.lower().split()) & set(ctx.lower().split())))
'
```
```
2026-10-04 22:22:24 [info     ] faithfulness_checked           claims_count=1 score=0.0 supported_count=0
issue example: 0.0
2026-10-04 22:22:24 [info     ] faithfulness_checked           claims_count=1 score=1.0 supported_count=1
control:       1.0
token overlap: ['and', 'docker']
```

**Expected vs. actual**
- Expected: the claim "The developer has Python, JavaScript, and Docker experience." is supported by three chunks that each mention one of those skills, so the score is above 0.5. "Knows Python" against "python expert" should also count as supported.
- Actual: the score is `0.0` with `supported_count=0`. The issue's example scores `0.0`, and the control, which repeats the claim's exact words, scores `1.0`. This matches the issue.

**What I looked at, not yet a diagnosis**

In the step 3 token overlap, `and` is in the stop-word list, so only one meaningful token is left, and the check needs two. `python,` and `javascript,` keep their trailing commas after `split()`, so they never match `python` or `javascript` in the context. My working hypothesis is that punctuation-attached tokens and the two-word threshold together cause this. I haven't tested a change yet. The issue also says support "should not depend on shared wording" (e.g. "Knows Python" vs "python expert", which share one word). Stripping punctuation alone wouldn't satisfy that, so I'll read how the checker is meant to treat partial matches before proposing anything.


## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Full run, rubric v1: `agreement: 17/20 scored items  (bar: 18/20: below the bar)`, `categories: clear-accept 5/8  disclosure 1/1  no-evidence 4/4  unfollowable-comms 3/3  wrong-target 4/4`. Disagreements: `pkg-03  accept  reject   NO     failed: claims-match-evidence, control-run`, `pkg-05  accept  reject   NO     failed: steps-rerunnable, control-run, template-coverage`, `pkg-12  accept  reject   NO     failed: steps-rerunnable, control-run, template-coverage`.
2. Partial run after loosening `steps-rerunnable` and `claims-match-evidence`, `--only pkg-03,pkg-05,pkg-12,pkg-06,pkg-15,pkg-18,pkg-20` (the three misses plus four canaries): `agreement: 7/7 scored items` (partial; not a bar run).
3. Full confirming run, rubric v2, saved with `--save-run eval-run.txt`: `agreement: 20/20 scored items  (bar: 18/20: PASS)`, `categories: clear-accept 8/8  disclosure 1/1  no-evidence 4/4  unfollowable-comms 3/3  wrong-target 4/4`.

**Package analysis**

`pkg-12` (prettier/prettier#19795, range formatting appends a stray token). Gold label: `accept`. My rubric in run 1: `reject`, `failed: steps-rerunnable` (control-run and template-coverage also failed, but they are preferred and can't change the verdict). Run 3: `accept`.

The grader's evidence in run 1 was: "Report says 'ran the issue's script verbatim' but the issue has no script; repro.mjs and the input strings are not shown or quoted in the report, so it can't be re-run from the package". The literal part of that is true. The report shows `$ node repro.mjs` and describes the file as "the issue's two `prettier.format` calls (shape A: rangeStart 19, rangeEnd 32; shape B: rangeStart 18, rangeEnd 31; both `parser: \"babel\"`)" rather than pasting it. But the issue body states both inputs verbatim (`beforeEach(() => {\n  // c\n  a();\n});\n` and `const f = () => {\n  // c\n  a();\n};\n`) with the same offsets and parser, so a stranger holding the issue and the report has every byte needed to rebuild `repro.mjs`. The artifacts then show the issue's exact corruption (`};);`, with a `SyntaxError` when re-parsed, and `};;`) on 3.9.6, with the version difference from 3.8.4 stated.

My v1 wording, "the exact commands and inputs are given or quoted", made the grader judge the report on its own, as if the issue weren't part of the package. That's a structure check ("did you paste it?") posing as an outcome check ("could someone re-run it?"). The gold label is right, and the same reading had also sunk pkg-05, whose `env.yml` is described by its only relevant feature (a `category:` section).

**Check rationale**

Check `steps-rerunnable` (required), as it reads now:

> | steps-rerunnable | The repro report's steps: commands, inputs, config files, and code snippets, read against the issue's trigger. | A stranger with the recorded environment could re-run the attempt from what is shown plus the issue itself: the commands are given, and each input is either shown, taken explicitly from the issue where the issue states it verbatim ("the exact 12 lines from the issue", "the issue's two calls"), or described precisely enough to recreate unambiguously (a minimal file whose only relevant feature is named). Fails when anything depends on private code, an unshared config, or an unnamed setup ("set up the project", "in our monorepo"), or when the step that performs the issue's trigger is missing. | required |

Why it reads this way: v1 said "the exact commands and inputs are given or quoted", and that failed three clear accepts for not pasting things that were already public. The question a maintainer actually asks is "can I run this myself?", so the pass condition now names the three ways an input can be available to a stranger. The fail side still names what really blocks a re-run, and those phrases come from the packages that should fail: pkg-18 reproduces only inside "a private monorepo with an unshared config", and pkg-06 leaves out the driver on a Windows-specific issue. I kept "the step that performs the issue's trigger is missing" as its own fail condition, so a report that skips the trigger can't pass just because its other steps are clear. I considered loosening further, to "steps are described", and rejected it: "set up the project" is a description too, and it is exactly what this check exists to stop.

**Trade-offs**

Loosening `steps-rerunnable` gives up some strictness. A report can now pass by pointing at the issue ("the issue's two calls") instead of pasting its inputs, so if the issue is later edited, or its inputs were ambiguous to begin with, a "described" input could rebuild something slightly different from what the reporter ran. I accept that miss: the alternative failed three of eight clear accepts.

To make sure the loosening didn't let real failures through, I ran an `--only` canary list before the full run: pkg-18 (private monorepo, the case this check exists for), pkg-06 (no environment record, unfollowable-comms), pkg-15 (unbacked root-cause claim, which touches the `claims-match-evidence` change made in the same revision), and pkg-20 (the one-package disclosure category, so the category floor couldn't silently break). All four stayed `reject` while pkg-03, pkg-05 and pkg-12 flipped to `accept` (`agreement: 7/7 scored items`). The confirming full run then held at 20/20 with every category matched, so nothing else moved.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.

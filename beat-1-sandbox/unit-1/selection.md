# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/59

**Verdict output**

Live-mode run on 2026-10-04: `claude "issue-select: grade these candidate first issues: .../issues/59 .../issues/55 .../issues/12"`. Issue #59 is ranked first and its verdict is `accept`.

````
All three issues pass every required check, so all are accepted. I couldn't run `gh` this session because it needed approval, so all evidence comes from GitHub's public API.

**Evidence that applies to all three:**
- **Maintainer-alive:** the last 5 commits on `main` are all by Aburke225, a human; the newest is 2026-09-16, 18 days ago.
- **Repo-in-use:** the repo isn't archived and was last pushed 2026-09-16. There are no releases, but the push date alone passes.
- **AI policy:** `docs/CONTRIBUTING.md`, the PR template and the README say nothing about AI, and silence passes.
- **Not claimed:** no assignees and no open PR linked or mentioned for any of the three. Each thread has 3–4 student claim comments, but the Path Review house rule says those don't block.
- **Maintainer-responsive** (preferred, ranking only): fails for all three. No maintainer has commented in any of the three threads, nor in #73's long thread.

**Ranked by your fit profile:**

1. **#59: Faithfulness checker scores claims unsupported when the context uses different words.** Best fit. It's in the `rag/` faithfulness checker, which evaluates LLM output, and it has a failing test to anchor the fix (`test_multiple_context_chunks`). It's one bug in one function, `_is_supported()`. Znasif posted a fix plan in the thread today; that's useful reading, and under the house rule it doesn't block you.
2. **#55: Skill extractor fails to detect JavaScript and TypeScript.** This is in ingestion, which feeds the RAG pipeline, and it has 5 failing tests. The JS/TS, tools and databases gaps all sit in one feature, so the rubric counts it as one fix, not an umbrella issue.
3. **#12: Add snapshot tests for prompt templates.** It's the only one with a `good first issue` label, but it's a test/CI enhancement with no failing test to fix, which your profile ranks lower. It's accepted because a maintainer opened it and labeled it.

On the friendly-label check, I graded the `tier-1` label on #59 and #55 as unclear. It might be the course's easiest-difficulty tier, but it isn't a standard newcomer label. Since that check is preferred, it doesn't change any verdict.

```json
[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/59",
    "checks": [
      {"name": "maintainer-alive", "grade": "pass", "evidence": "Last default-branch commit 2026-09-16 by human Aburke225, 18 days ago"},
      {"name": "repo-in-use", "grade": "pass", "evidence": "archived: false; last push 2026-09-16 (within 90 days); no releases"},
      {"name": "not-claimed", "grade": "pass", "evidence": "No assignees; cross-refs are issues #56/#60, not PRs; no open PR mentions #59; student claim comments ignored per house rule"},
      {"name": "bounded-scope", "grade": "pass", "evidence": "Single bug in _is_supported() in faithfulness_checker.py; no maintainer design or core-internals comment"},
      {"name": "work-defined", "grade": "pass", "evidence": "Bug report (label: bug) with repro: test_multiple_context_chunks fails 'assert 0.0 > 0.5'"},
      {"name": "ai-policy-allows", "grade": "pass", "evidence": "docs/CONTRIBUTING.md and PR template contain no AI policy"},
      {"name": "maintainer-responsive", "grade": "fail", "evidence": "No maintainer comments in sampled threads #73, #59, #55, #12"},
      {"name": "friendly-label", "grade": "unclear", "evidence": "Labels: bug, rag, tier-1; tier-1 is not an explicit newcomer label"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/55",
    "checks": [
      {"name": "maintainer-alive", "grade": "pass", "evidence": "Last default-branch commit 2026-09-16 by human Aburke225, 18 days ago"},
      {"name": "repo-in-use", "grade": "pass", "evidence": "archived: false; last push 2026-09-16 (within 90 days); no releases"},
      {"name": "not-claimed", "grade": "pass", "evidence": "No assignees, no cross-referenced or linked PRs; student claim comments ignored per house rule"},
      {"name": "bounded-scope", "grade": "pass", "evidence": "One detection-gap bug in skill_extractor.py naming several instances of the same defect; not an umbrella"},
      {"name": "work-defined", "grade": "pass", "evidence": "Bug report (label: bug); 5 named tests xfail in tests/unit/test_skill_extractor.py"},
      {"name": "ai-policy-allows", "grade": "pass", "evidence": "docs/CONTRIBUTING.md and PR template contain no AI policy"},
      {"name": "maintainer-responsive", "grade": "fail", "evidence": "No maintainer comments in sampled threads #73, #59, #55, #12"},
      {"name": "friendly-label", "grade": "unclear", "evidence": "Labels: bug, ingestion, tier-1; tier-1 is not an explicit newcomer label"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/12",
    "checks": [
      {"name": "maintainer-alive", "grade": "pass", "evidence": "Last default-branch commit 2026-09-16 by human Aburke225, 18 days ago"},
      {"name": "repo-in-use", "grade": "pass", "evidence": "archived: false; last push 2026-09-16 (within 90 days); no releases"},
      {"name": "not-claimed", "grade": "pass", "evidence": "No assignees, no cross-referenced or linked PRs; FikerK96 plans a branch but no PR opened; claims ignored per house rule"},
      {"name": "bounded-scope", "grade": "pass", "evidence": "Single task: snapshot tests in tests/unit/test_prompt_templates.py, est. 3-5 hours"},
      {"name": "work-defined", "grade": "pass", "evidence": "Enhancement opened by COLLABORATOR Aburke225 and labeled 'good first issue'"},
      {"name": "ai-policy-allows", "grade": "pass", "evidence": "docs/CONTRIBUTING.md and PR template contain no AI policy"},
      {"name": "maintainer-responsive", "grade": "fail", "evidence": "No maintainer comments in sampled threads #73, #59, #55, #12"},
      {"name": "friendly-label", "grade": "pass", "evidence": "Carries 'good first issue' label"}
    ],
    "verdict": "accept"
  }
]
```
````

---

## Eval iterations

**Run history**

1. Full run, rubric v1: `agreement: 19/20 scored items  (bar: 18/20: PASS)`, `categories: claimed 4/4  clear-accept 7/8  dead-repo 3/3  policy 1/1  scope 4/4`. The one disagreement: `issue-04  accept  reject   NO     failed: bounded-scope`.
2. Partial re-run after revising `bounded-scope` clause (a), `--only issue-04,issue-05,issue-10`: `agreement: 3/3 scored items` (partial; not a bar run). issue-04 flipped to accept; the two umbrella canaries stayed reject.
3. Full confirming run, rubric v2, saved with `--save-run eval-run.txt`: `agreement: 20/20 scored items  (bar: 18/20: PASS)`, `categories: claimed 4/4  clear-accept 8/8  dead-repo 3/3  policy 1/1  scope 4/4`.

**Issue analysis**

`issue-04` (zxcalc/zxlive#555, "Missing several basic rule previews"). Gold label: `accept`. My rubric in run 1: `reject`. Run 3: `accept`.

In run 1 the only required check that failed was `bounded-scope`, and the grader's evidence was: "'Missing several basic rule previews. Including remove identity, fuse spiders, remove self loops, etc.' is an open-ended list of sub-items (umbrella pattern, clause a)". Every other required check passed. The repo is alive (commits on 2026-08-04 by RazinShaikh and 96-LB), it isn't archived, and it had a release on 2026-04-29. The issue has no assignee, no PRs and no comments. A COLLABORATOR filed it with the `Type: bug` and `good first issue` labels, and no AI policy is stated.

The rubric's v1 clause (a) only said "not an umbrella, tracking, or 'megaissue' list of sub-items". That wording let the grader treat any list as an umbrella. But the body of #555 is not a tracker. It is one bug, missing previews, in one feature, the proof-mode rule sidebar, and it lists a few instances of that bug. It doesn't link other issues or ask for separate PRs, and a maintainer filed it with a good-first-issue label. Fixing it is one PR in one place. The gold label is right, and my clause was too blunt to tell "a list of instances of one defect" apart from "a list of separate work items". I rewrote clause (a) to define an umbrella by its sub-items being meant to become separate issues or PRs, and the issue then passed. The real umbrellas (issue-05, issue-10) still failed.

**Check rationale**

Check: `bounded-scope` (required). Its current wording in `tools/issue-select/rubric.md`:

> All of: (a) the issue is not an umbrella, tracking, or "megaissue" whose sub-items are meant to become separate issues or PRs (it links other issues, calls itself a tracker, or invites many PRs), and not an open-ended incremental effort across the codebase ("PRs welcome both big and small"); one bug that names several instances of the same defect in one feature (e.g. "missing several previews: A, B, C") is one fix, not an umbrella; (b) it is not a pure usage/support question; (c) no maintainer says the fix needs changes to core internals or an undecided design, and the thread does not show an unresolved design debate; (d) fewer than 2 closed-unmerged PRs in its history when the issue is more than 1 year old. A short body, missing repro steps, or a maintainer listing named causes and optional extra suggestions does NOT fail this check.

Why it has this form: the scope family is the one that lives in the issue text instead of in the repo-facts numbers, so this check has to turn judgment calls into tests someone else could apply.
- Clause (a) defines an umbrella by its observable signals: links to other issues, calling itself a tracker, or inviting many PRs. "Has a list" isn't one of them; that was exactly what caught issue-04. The "PRs welcome both big and small" phrase is quoted from issue-05 (sympy's typing effort), which has a good-first-issue label but no end.
- Clause (d) puts a number on "years of abandoned attempts": 2 or more closed, unmerged PRs on an issue more than a year old. That is the pattern in issue-15 (zulip#19589, open since 2021 with #20840 and #23123 closed). It doesn't catch issue-09 (conda#7617), which has only one closed PR and a maintainer still inviting takers.
- The closing "does NOT fail" sentence protects terse but bounded maintainer bugs like issue-11 and issue-19 (a maintainer naming two causes plus an optional multiprocessing suggestion), which the evidence guide warns against punishing for polish.

**Trade-offs**

Narrowing clause (a) gives up some sensitivity to umbrellas that don't announce themselves. Suppose an issue lists several same-kind items that are really independent pieces of work, each its own PR, but never links other issues, calls itself a tracker, or invites multiple PRs. The new wording will now call it "one fix" and pass it. I accept that miss: in the eval set every real umbrella announces itself, and both of them still fail. I checked that with an `--only issue-04,issue-05,issue-10` canary run: issue-05 ("PRs welcome both big and small") and issue-10 ("Documentation request megaissue", a list of linked issues) both stayed `reject`, while issue-04 flipped to `accept`. The confirming full run then held at 20/20 with every category matched, so the change didn't move any other verdict. The same trade-off showed up in live mode: on Path Review #55 the grader passed a list of skill-detection gaps (JS/TS, tools, databases) as "several instances of the same defect". That reading is defensible, but it is the kind of borderline call the old wording would have rejected.

---

## Selection rationale

Graded on whether all three are answered, in your own words. Not on how good the
reasoning is, and not on length — a short honest answer to each earns the full marks.
This is also the basis for the claim comment you write in Unit 2.

**Selection rationale**

1. **Fit and time.** #59 is in the `rag/` faithfulness checker, which judges whether a generated claim is supported by the retrieved context. That is the RAG and LLM-evaluation side I want to learn. It's one function (`_is_supported()`) with a failing test (`test_multiple_context_chunks`, `assert 0.0 > 0.5`) to tell me when I'm done, so it fits in the time I have for a first contribution.
2. **What the verdict got right, and what I weighed that it couldn't.** The skill correctly saw that the repo is alive, the issue is one bounded bug with a reproduction, and nobody has an open PR on it. In my first live run it also correctly rejected #60, #68 and #69, because classmates already have open PRs on them. What the rubric can't weigh is how hard the fix really is. The issue says "Support should not depend on shared wording", which could be met by a small normalisation fix (stemming, lowercase, stop words) or a real semantic-matching change. I'll only know which after I reproduce it and read the scorer. It also can't tell me that the maintainer hasn't commented on any of these threads (the maintainer-responsive preferred check failed), so I may get little guidance before I open a PR.
3. **Expected difficulty in claiming it.** Several classmates have already left claim comments, and one posted a fix plan today. The house rule says that doesn't block me, but my claim comment needs to add something new (my own reproduction and a different angle on the fix) rather than repeat theirs. Someone could also open a PR before I do, which my own rubric would then count as claimed, so I should claim and reproduce early in Unit 2.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.

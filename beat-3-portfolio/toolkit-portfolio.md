# Toolkit Portfolio

Path: `beat-3-portfolio/toolkit-portfolio.md`

**Student:** Mohtashim Syed

## Featured Tools

I chose the three tools that I used on real work and that changed because of it. issue-scout found the issue I contributed to (deepeval#3629), and I changed its search strategy and rubric twice along the way. repro-check and plan-check are two more of the four Beat 1 graders. Their evals missed on the first full run, and I had to decide which of my own criteria were wrong. pr-precheck is in the inventory because the PR it graded is still open, so I have little to show for it beyond its eval.

**issue-scout**

**What it is, and the judgment hole I wrote:** It searches GitHub for first-contribution issues with `gh search issues`, ranks the results using search fields only, and grades the top five against a written rubric. The judgment is mine: the search strategy (17 Python RAG/LLM and document-parsing repos, no assignee, updated within 180 days, bugs first, fewest comments) and the rubric, especially the `bounded-scope` check.

**How it evolved, dated:**
- 2026-10-18, scan 1: it rejected 5 of 5. Four of the five had open PRs that my ranking could not see. I added `--no-assignee` and `-- -linked:pr` to every query, which took the good-first-issue query over the RAG repos from 45 results to 11.
- 2026-10-18: my `doc` label filter matched `docling-document` by substring and silently dropped every docling issue. I changed it to match whole label names. Scan 2 then accepted 3 of 5, and none of the five had an open PR.
- 2026-10-18: scan 2 accepted docling#1705 even though a 2026-09-18 comment said "does not reproduce on current main … so it looks fixed". I added clause (e) to `bounded-scope`, then re-ran the Unit 1 harness on the 8 gold-accept bundles: `agreement: 8/8`, and docling#1705 now rejects.
- 2026-11-11, scan 3: 59 results, 11 after ranking, 1 accept. Clause (e) rejected docling#1705 again and docling#4147 ("known-and-fixed data race in docling-parse"). The accept was deepeval#3629.

**Evidence:** The `bounded-scope` rule before and after the patch.

Before (Unit 1, `tools/issue-select/rubric.md`):
```
(d) fewer than 2 closed-unmerged PRs in its history when the issue is more than 1 year old.
```
After (2026-10-18):
```
(d) fewer than 2 closed-unmerged PRs in its history when the issue is more than 1 year old;
(e) no comment from the last 90 days reports that the problem no longer reproduces on the current
default branch or latest release, unless a later comment shows it still does.
```
The issue it found led to a real PR, [deepeval#3634](https://github.com/confident-ai/deepeval/pull/3634). That PR is open and has had no maintainer review. The issue's opener (a contributor, not a maintainer) wrote that "your reading matches mine, and you found a sharper layer than my original report."

**repro-check**

**What it is, and the judgment hole I wrote:** It grades a reproduction package (a claim comment plus a repro report, read against its issue) and decides whether it is ready to post upstream. The judgment is my rubric: whether the claim matches the evidence, whether the steps can be re-run, whether there is a control run, and whether the report follows the repo's template.

**How it evolved, dated:**
- 2026-10-04: the first full run on rubric v1 scored `agreement: 17/20 (bar: 18/20: below the bar)`. It rejected three gold accepts, pkg-03, pkg-05 and pkg-12, on `claims-match-evidence`, `steps-rerunnable`, `control-run` and `template-coverage`.
- 2026-10-04: I loosened `steps-rerunnable` and `claims-match-evidence`, because they were stricter than what a reader needs to re-run the repro. A partial run on the three misses plus four canaries then gave `7/7`, and a full confirming run on rubric v2 scored `agreement: 20/20` (bar: 18/20: PASS).

**Evidence:** The v1 miss and the partial re-run, from `beat-1-sandbox/unit-2/reproduction.md`:
```
Full run, rubric v1: agreement: 17/20 scored items  (bar: 18/20: below the bar)
pkg-03  accept  reject   NO     failed: claims-match-evidence, control-run
After loosening steps-rerunnable and claims-match-evidence, --only pkg-03,pkg-05,pkg-12,pkg-06,pkg-15,pkg-18,pkg-20:
agreement: 7/7 scored items  (partial; not a bar run)
Full run, rubric v2: agreement: 20/20 scored items  (bar: 18/20: PASS)
```
I also used the same repro habits on deepeval#3629. My first stub-judge run printed `None` for every window reason, because they only exist in `verbose_logs`. I changed the script to read them there, and the before/after reasons that came out are in the repro commit `15037a5` on my fork.

**plan-check**

**What it is, and the judgment hole I wrote:** It grades a plan, together with the comment that will announce it, against the issue and the reproduction evidence, and decides whether it is ready to post and build from. The judgment is my rubric and `procedure.md`: grounded diagnosis, bounded change, an executable plan, a decisive test, and engagement with the thread and the repo's conventions.

**How it evolved, dated:**
- 2026-10-04: the first full run on v1 scored `agreement: 19/20`, which passes the bar. One gold accept, pkg-14, was rejected on `executable` and `honest-unknowns`.
- 2026-10-04: I loosened `executable`, and a partial run on pkg-14 plus three reject canaries gave `4/4`. A full confirming run then scored `agreement: 20/20`. This one barely changed, because the first run already passed. I would not claim more than that.

**Evidence:** A catch on a real plan, from a plan I graded in my practice repo. The tool flagged scope that nothing motivated, and it named a gap in its own procedure:
```
bounded-change: pass. ... One borderline item: the plan adds 11 stop words (`with`, `on`, `they`…)
and moves the list to a module constant, but no repro or test motivates either. Give a reason or drop them.
...
Gaps in your procedure
- No step reads the house rules in `scope.md` (piggybacking, branch naming), so the overlap with
  Znasif's plan isn't graded anywhere.
```
For deepeval#3629, the plan itself changed after I read the code. The issue implied changing a 1.0 score to 0. I found a test that pins 1.0 on purpose, so my claim comment offered option 1 (fix the false reason, keep the scores) instead. The fix is commit `a311ce2` with 14 new tests, 11 of which fail without it.

## Inventory

- **pr-precheck:** grades a PR (title, description and diff read against the plan and issue) before I open it. It passed its eval at 18/20 on 2026-10-04, then 7/7 on a partial run after I loosened `decisive-evidence`, and 20/20 on a full confirming run. I did not run it on deepeval#3634, so it stands as an eval result only.
- **Claude Code with `gh`:** reads issue threads and code on `main` and builds the repro environment. It found the test that pins faithfulness at 1.0.
- **Repro script with a stub judge (`repro/issue_3629.py`):** a `DeepEvalBaseLLM` with no provider, modeled on deepeval's own test. It reproduces the bug without an API key. It is committed as `15037a5`.
- **black 25.1.0 (pinned):** the newest black reformatted an unrelated block in my diff. I reverted and used the version in deepeval's `pyproject.toml`.
- **Unit 6–8 tools (voice-guard, ship-gate, pr-monitor):** not built. They do not exist on my machine, so I have nothing to report from them.

## The Arc

**Beat 1**
Date: 2026-10-04
Friction: the repro-check, plan-check and pr-precheck rubrics (v1) rejected gold accepts in the eval (17/20 with three misses, 19/20 with one, and 18/20 with two). I saw it in the harness output.
Change: I loosened the failing criteria (`steps-rerunnable`, `claims-match-evidence`, `executable`, `decisive-evidence`) and re-ran only the misses and canaries.
Result: the partial runs agreed 7/7, 4/4 and 7/7, and the reject categories kept their canary agreement. The full confirming runs then scored 20/20 for all three tools.

**Beat 2**
Date: 2026-10-18
Friction: scan 1 of issue-scout rejected 5 of 5. Four had open PRs, and my `doc` label filter silently dropped every docling issue.
Change: I added `--no-assignee` and `-- -linked:pr` to every query and matched label names whole.
Result: scan 2 accepted 3 of 5, and none of the five had an open PR.

**Beat 3**
Date: 2026-10-18
Friction: scan 2 accepted docling#1705, which a comment said no longer reproduces on main.
Change: I added clause (e) to the `bounded-scope` rubric check.
Result: the Unit 1 harness on the 8 gold accepts agreed 8/8, and docling#1705 flipped to reject. On 2026-11-11 the clause also rejected docling#4147.

**Beat 4**
Date: 2026-11-11
Friction: my scan-2 accepts had gone stale. markitdown#2288 had an open PR and docling#3170 turned out to be a bug in docling-parse (C++).
Change: I re-ran the scout (scan 3) and read the code on deepeval `main` before claiming the one accept, #3629.
Result: I found a test that pins faithfulness at 1.0, so the claim offered a reasons-only fix. I opened PR #3634 (fix `a311ce2`, 14 tests, 11 failing without the fix). The issue's opener confirmed the reading and reviewed the PR on 2026-11-12. There has been no merge and no maintainer review.

**Beat 5**
Date: 2026-11-11
Friction: my first repro run printed `None` for every window reason, and the PR's `pr-title-check` failed with "No release type found in pull request title".
Change: I read the reasons from `verbose_logs` in the script, and I retitled the PR with a `fix:` prefix.
Result: the repro showed the wrong reason before the fix and the corrected one after it, and `pr-title-check` passed.

## Limitations and What's Next

The toolkit stops at the opened PR. I built no tool for what happens after it: reading maintainer feedback, drafting replies, or running my own pre-submit checks on the real PR. The Unit 6–8 tools do not exist, so the toolkit has no record from that stage. My PR is still open with no maintainer review, and three CI checks fail for reasons outside it (one unrelated sqlite test, lint on two untouched tracing test files, and a Vercel deploy that needs a team member). The fix also leaves faithfulness unable to tell "nothing to check" from "extraction failed": the reason now says so, but the score is still 1.0. One reviewer suggestion is not acted on yet: a stable marker in the reason so tooling can count these cases.

Next I would give issue-scout a memory of past rejects, since 2 of 5 grading slots in scan 3 were spent re-grading them. I would add a rubric check for "the fix the issue implies conflicts with an existing test", which is what I caught by hand on #3629. I would run pr-precheck on my real PR before opening the next one.

**Tool limits.**

issue-scout: it has no memory of past rejects (2 of 5 grading slots wasted in scan 3), its accepts go stale within about 3 weeks, and the rubric has no check for an implied fix that conflicts with an existing test.
repro-check: I loosened its criteria to pass the eval, and its 20/20 is on 20 practice packages only, so I can't say how strict it is on reproductions outside my practice set.
plan-check: it graded a practice plan and never my real deepeval plan, and its own output noted that its procedure does not read the house rules in `scope.md`, such as overlap with another student's plan.

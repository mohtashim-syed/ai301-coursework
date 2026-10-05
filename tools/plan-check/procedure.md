# Procedure: how this skill grades a plan package

## Read order

1. Read the issue (title, body) and write down, in one line, the
   behavior the issue reports and any version/platform it names.
2. Read the repro evidence next, before the plan. Write down: (a) the
   observed result, (b) every control run and what it shows works
   correctly, (c) any artifact that shows where in the pipeline the
   fault already exists. You read this before the plan so the plan's
   cause is tested against the evidence instead of the evidence being
   read through the plan's story.
3. Read the thread highlights (live: the issue's comments). Write down
   each maintainer direction: an isolated culprit or file, a posted
   patch or test request, a settled approach, open or prior PRs.
4. Read the repo-facts contribution policy (live: `CONTRIBUTING.md`
   and any AI policy file). Write down whether it requires AI-use
   disclosure, and whether that duty covers comments.
5. Only now read the candidate plan, then the candidate plan comment.

## Evidence gathering

1. For `grounded-diagnosis`: quote the plan's cause sentence. Next to
   it, list the controls and artifacts from Read order step 2 that bear
   on it, and any culprit from step 3.
2. For `bounded-change`: list every distinct piece of work the plan
   says it will do, one per line. Mark each line "fix", "test for the
   fix", "deferred/out of scope", or "extra" (anything else).
3. For `executable`: copy every file, function, or code site the plan
   names, and the sentence that states the approach. Note any phrase
   that leaves a choice open ("or", "whichever", "somewhere", "not
   sure", "investigate then decide").
4. For `decisive-test`: copy the test plan's expected outcome(s) and
   note which repro step or named test each maps to.
5. For `thread-and-convention`: put the plan comment next to the
   maintainer directions from Read order step 3 and the policy note
   from step 4. Record whether the comment mentions each direction and
   whether it contains an AI-use disclosure.
6. For `honest-unknowns`: copy the plan's risks/unknowns and any
   certainty words ("definitely", "will", "the cause is") attached to
   something the evidence does not show.
7. Live mode: the repro evidence is the student's posted repro comment
   on the issue; the plan is `plan.md`, the comment is the draft
   comment file. Grade only what the drafts contain and quote.

## Check execution

1. Run the required checks in table order, then the preferred one.
2. Grade each check only against the evidence recorded for it in
   Evidence gathering, applying the rubric's pass condition literally.
   Do not re-read the whole package unless a recorded quote is
   ambiguous.
3. `grounded-diagnosis`: fail if any recorded control or artifact rules
   the cause out, even if the plan is otherwise strong.
4. `bounded-change`: fail if any line was marked "extra". Deferred
   items never fail it.
5. `executable`: fail if no file/function/site is recorded, or if an
   open-choice phrase governs the approach itself.
6. `decisive-test`: fail if no recorded outcome maps to the repro or a
   named test of the fix.
7. `thread-and-convention`: fail if any maintainer direction goes
   unmentioned in the comment and the plan contradicts or ignores it,
   or if the policy requires comment disclosure and none is present.
8. If the evidence a check needs is genuinely absent from the package
   (not merely unread), grade it `unclear` and say what is missing.
9. Write one evidence line per check: the quote or fact that decided
   it.

## Verdict assembly

1. Apply the rubric's verdict rule: accept only if every required check
   is `pass`.
2. Any required `fail` or `unclear` makes the verdict reject.
3. Ignore the preferred check's grade for the verdict; report it.
4. In the summary, name the deciding check for a reject (the first
   failing required check in table order) and quote its evidence line.
5. Live mode: after the verdict, hold the plan comment against
   `voice-guide.md` and list any broken rule; this never changes the
   verdict.
6. Emit the JSON block last, with every check listed.

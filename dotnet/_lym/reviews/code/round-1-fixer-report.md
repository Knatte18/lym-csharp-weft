# Round 1 fixer-report

## Changes

- F1: added `TransitionalFactorMatchesReferenceValue` to `ChurchillFrictionTests.cs`, at Re 3000 and relative roughness 0.0001. Existing rows are unchanged.
  Fixed differently from the proposal: the `fix` key proposed asserting the factor above 64/Re and within 5 percent of `ColebrookFriction`.
  I did not make that change.
  The self-check forks showed by hand arithmetic that a 5 percent bound passes with `transitionTerm` removed (about 0.0449, 3 percent from Colebrook), and the greater-than-64/Re assertion passes for any plausible mutant.
  The test instead pins the value 0.04305 within 1 percent, which is the model's output at that point and about 1.4 percent from Colebrook (hand-solved 0.0436).
  Removing `transitionTerm` gives about 0.0449, 4 percent away, so the row fails; a constant of 40000 gives about 0.0411 and also fails.
- Commits: first the test as proposed, then the pinned-value correction.

## Self-check

- fix-code-correctness, MEDIUM: the 5 percent bound does not catch a dropped `transitionTerm`. Fixed with my own change (pinned value, 1 percent).
- fix-proposal, MEDIUM: same finding, merged with the one above. Fixed with the same change.
- fix-proposal, LOW: `Is.GreaterThan(64.0 / 3000.0)` adds nothing. Fixed by removing the assertion.
- fix-consistency: no findings.
- fix-affected-set: no findings.

## Disputed

None.

## Deferred

None.

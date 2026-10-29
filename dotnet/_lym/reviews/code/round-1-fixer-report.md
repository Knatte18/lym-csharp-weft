# Fixer-report, round 1 (code review)

## Changes

Both changes are in `tests/LymCsharp.Hydraulics.Tests/ColebrookFrictionTests.cs`, one commit each, fixed as proposed.

- **F1** (commit `afd0d41`): `ConvergesAcrossReynoldsAndRoughnessGrid` now steps an integer index over 440 steps, so the first point is exactly Re 4000 and the last exactly Re 1e8. Verified with `lym gate test` tier 1 (Slow): passed.
- **F2** (commit `ee8f23f`): added `NonConvergingInputThrows`, asserting `InvalidOperationException` for `FrictionFactor(double.NaN, 0.001)`. Verified with `lym gate test` tier 0: 102 passed, 0 failed.

## Self-check

- fix-code-correctness: no findings.
- fix-consistency: no findings.
- fix-affected-set: no findings.
- fix-proposal, LOW: `NonConvergingInputThrows` depends on NaN reaching the loop, so a future NaN guard would break it. Disputed, `every fix worse`: see Disputed.
- fix-proposal: no finding on F1.

## Disputed

- **fix-proposal LOW on F2, ground `every fix worse`.**
  The premise holds, but no other input exhausts the cap.
  Every finite input in the physical range converges: the Slow sweep in `ConvergesAcrossReynoldsAndRoughnessGrid` asserts that across Re 4000 to 1e8 and roughness 0 to 0.05.
  So only a non-finite input reaches the throw, and any alternative row is either unreachable or needs a production change outside the review's scope.
  The review prescribed this exact input, and the fragility arises only if a later change adds a NaN guard, which would then update this test.

## Deferred

Nothing deferred.

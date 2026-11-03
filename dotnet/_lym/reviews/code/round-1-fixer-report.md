# Round 1 fixer-report

## Changes

- F1 (MEDIUM), fixed as proposed, commit `a75caba`.
  `tests/LymCsharp.Hydraulics.Tests/PipeFlowTests.cs` gains `RepeatedTurbulentFlowReachesFrictionModelOnce` and a nested `CountingFriction` stub.
  The test calls `Calculate` twice with `SteelPipe`, `Fluid.Water` and 10 L/s, and asserts the stub's call count is 1.
- F2 (LOW), fixed as proposed, commit `ceec8ba`.
  The `PipeFlowCalculator` doc in `src/LymCsharp.Hydraulics/PipeFlow.cs` now says the calculator remembers the friction factor of every flow solved at a Reynolds number of 2300 or more.

`lym gate test ./tests/LymCsharp.Hydraulics.Tests` passed before the commits: 78 passed, 0 failed.

## Self-check

All four forks returned reports, saved as `round-1-fixer-report.lens-<lens>.md`.

- fix-code-correctness: no findings.
- fix-consistency: no findings.
- fix-proposal: no findings.
- fix-affected-set: no findings.

## Disputed

None.

## Deferred

None.

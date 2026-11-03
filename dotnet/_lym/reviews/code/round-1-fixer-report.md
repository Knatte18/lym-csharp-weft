## Changes

- **F1** (LOW, fixed as proposed): added `ReynoldsRejectsNonPositiveFlow` to `tests/LymCsharp.Hydraulics.Tests/PipeFlowTests.cs`, after `NonPositiveFlowThrows`.
  It has `[TestCase(0.0)]` and `[TestCase(-1.0)]` and asserts `ArgumentOutOfRangeException` from `PipeFlowCalculator.Reynolds`.
  Committed as `3f81f55`; `lym gate test ./tests/LymCsharp.Hydraulics.Tests` passed.

## Disputed

None.

## Deferred

None.

## Self-check

All four forks returned a report. Each found nothing.

- fix-code-correctness: no findings.
- fix-consistency: no findings.
- fix-proposal: no findings.
- fix-affected-set: no findings.

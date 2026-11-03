# Drift baseline

## Scope

`full`.
`LymCsharp.Regression` references `LymCsharp.Units`, `LymCsharp.Hydraulics` and `LymCsharp.Reporting`, and the first and last of those both build `Quantity.cs`, which the task changes.
The suite can therefore see the change.
No test is skipped: the task removes no feature and adds no nondeterminism.

## Expected drift

None.
The task adds `Quantity.Parse` and tests for it, and states that nothing else changes: no caller uses `Parse`, and no calculation or report output moves.
Every line of every test in the suite must stay as it is.

## Risks

No stated output invariant contradicts the design.
The tests the task adds live in `LymCsharp.Units.Tests`, outside the regression suite.

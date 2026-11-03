# Drift baseline

Scope: `full`, no skips.

## Reasoning

`LymCsharp.Regression` references `LymCsharp.Hydraulics`, which holds `PipeFlow.cs`, the file the task changes.
`RegressionCases.cs` runs `PipeFlowCalculator.Calculate` in its pipe-flow cases, so the suite exercises the changed code.

The task extracts the inline Reynolds computation into a public static `PipeFlowCalculator.Reynolds` that `Calculate` calls.
The decision record requires every `Calculate` result to stay unchanged to the last digit, so the task is a pure refactor and nothing may drift.

## Expected drift

None.
Every case, pipe-flow and reporting alike, must compare clean.
The new `Reynolds` test lives in `LymCsharp.Hydraulics.Tests`, outside the regression suite.
Any `drift:` line in the compare is a regression: for example a changed operation order in the Reynolds formula, or a unit conversion moved across the new method boundary.

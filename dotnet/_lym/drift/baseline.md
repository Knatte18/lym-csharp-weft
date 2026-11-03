# Drift baseline

Scope: `full`, no skips.

## Reasoning

The capture command runs `LymCsharp.Regression`, which references `LymCsharp.Hydraulics`, `LymCsharp.Units` and `LymCsharp.Reporting`.
The task adds `ChurchillFriction` to `LymCsharp.Hydraulics` and appends one case to `RegressionCases.All` in `LymCsharp.Regression`, so the suite reaches the change.

The task changes no existing behavior on purpose.
`SwameeJainFriction`, `HaalandFriction`, `ColebrookFriction`, `PipeFlowCalculator`, `ResultFormatter` and the existing cases stay as they are, so every existing case must compare clean.

## Expected drift

No existing case should drift.
Each case in the baseline (`water-steel-dn100-swamee-jain`, `water-steel-dn100-haaland`, `oil-steel-dn100-laminar`, `water-pvc-2in-transitional`, `oil-steel-6in-turbulent`) must render byte-identical lines after the task.

The one expected difference is a new case that the baseline does not hold, which runs a pipe flow through `ChurchillFriction`.
The compare should report it as added, not as drift.
Any change to an existing case, or a missing one, is a regression.

Tests under `tests/` are outside the capture and are not part of this scope.

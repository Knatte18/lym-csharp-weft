# Drift baseline

## Scope

`partial`, with every regression case skipped.

## Reasoning

The capture command builds `src/LymCsharp.Regression`, which references `LymCsharp.Reporting`.
The task changes `ResultFormatter` in `src/LymCsharp.Reporting/ResultFormatter.cs`, so the suite reaches the change.

Every case in `RegressionCases.All` renders its last line as `report = {ResultFormatter.Format(result, pressureUnit)}`.
The task changes that output on purpose: every numeric value in the report goes to 4 significant digits.
The harness skips whole cases and reports only the first deviating line of each case, so a case cannot be partly skipped.
All cases therefore drift, and all are skipped.

## Expected drift

Only the `report = ...` line (line 12) of each case changes:

- `water-steel-dn100-swamee-jain`
- `water-steel-dn100-haaland`
- `oil-steel-dn100-laminar`
- `water-pvc-2in-transitional`
- `oil-steel-6in-turbulent`
- `water-pvc-2in-transitional-churchill`

The change touches velocity (`0.###` today), Reynolds number (`0` today) and the pressure drop `Quantity` text.
The raw lines (`velocity_m_per_s`, `reynolds`, `friction_factor`, `pressure_drop_pa` and the inputs) come from `PipeFlowCalculator` and must not change.
A later compare that shows drift in any raw line of these cases would be a regression, but the skip hides it, so the formatter's own tests and a manual look at the raw lines have to cover that.

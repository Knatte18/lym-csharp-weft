# Drift baseline

Scope: `full`, no skipped tests.

## Reasoning

The capture command runs `LymCsharp.Regression`, which references `LymCsharp.Reporting`.
The task changes `ResultFormatter.Format` in `LymCsharp.Reporting`, and `RegressionCases.PipeFlowCase` calls it for the `report = …` line of every case.
The suite therefore reaches the change.
The task does not state an output invariant that contradicts its design: it names the output change as intended.
No test becomes meaningless or nondeterministic, so nothing is skipped.

## Expected drift

Two cases have a transitional flow and drift:

- `water-pvc-2in-transitional`
- `water-pvc-2in-transitional-churchill`

In each, only the `report = …` line is expected to change.
`Format` appends ` (transitional: friction factor uncertain)` to the end of the line when `result.Regime` is `FlowRegime.Transitional`.
Every other line of both cases must stay as it is, including `regime`, `reynolds`, `friction_factor` and `pressure_drop_pa`, since the change touches formatting only and not the calculation.

## Expected no drift

Every line of these cases must stay as it is, `report = …` included, because their regimes are laminar or turbulent and `Format` leaves those lines unchanged:

- `water-steel-dn100-swamee-jain`
- `water-steel-dn100-haaland`
- `oil-steel-dn100-laminar`
- `oil-steel-6in-turbulent`

## Skipped tests

None.

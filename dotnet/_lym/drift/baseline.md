# Drift baseline

## Scope: full

The capture command runs `LymCsharp.Regression`, which references `LymCsharp.Hydraulics`.
The task adds `CachedFrictionModel` there and changes `PipeFlowCalculator` in `PipeFlow.cs`, and every case in `RegressionCases.cs` goes through `PipeFlowCalculator.Calculate`.
The suite reaches the change, so nothing is skipped.

## Expected drift

The goal says every result stays unchanged, but the specified design rounds the Reynolds number to a whole number before calling the inner model at 2300 and above.
The friction factor is therefore evaluated at the rounded Reynolds number, not the exact one, and a non-integer Reynolds number can move `friction_factor` in the last digits.
Only `friction_factor`, `pressure_drop_pa` and `report` can move, and only in the cases at or above Re 2300:

- `water-steel-dn100-swamee-jain`
- `water-steel-dn100-haaland`
- `water-pvc-2in-transitional`
- `oil-steel-6in-turbulent`
- `water-pvc-2in-transitional-churchill`

The drift is tiny and may be zero for a case whose Reynolds number is already integral to the printed precision.
`pressure_drop_pa` follows `friction_factor` linearly, so it moves by the same relative amount.
`report` prints 4 significant digits, so it should drift only if the change crosses a rounding boundary.

Any drift in these cases is accepted with the reason "friction factor evaluated at the rounded Reynolds number".
A change larger than a relative 1e-3 in `friction_factor` is not explained by rounding and is a regression.

## Must stay as it is

- `oil-steel-dn100-laminar` (Re below 2300, inner model called directly): no line changes.
- In every case: `fluid`, `friction_model`, `diameter_m`, `length_m`, `roughness_m`, `flow`, `velocity_m_per_s`, `reynolds` and `regime` stay as they are.
  `reynolds` stays exact because `PipeFlowCalculator` still reports its own unrounded value.
- `friction_model` must still name the model the case passed in, never `CachedFrictionModel`, since the case reads the type from its own variable.

## Skipped tests

None.

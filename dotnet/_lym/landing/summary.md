# Add ChurchillFriction, valid in every flow regime

`SwameeJainFriction`, `HaalandFriction` and `ColebrookFriction` cover turbulent flow only.
`ChurchillFriction : IFrictionModel` in `LymCsharp.Hydraulics` implements Churchill's 1977 equation, one explicit formula for laminar, transitional and turbulent flow with no regime switch.
It gives `64/Re` at low Reynolds numbers and approximates Colebrook-White at high ones.

`RegressionCases` gains `water-pvc-2in-transitional-churchill`, a pipe flow through the new model.
The existing cases are unchanged.
The baseline does not hold the new case, so Drift should report it as new while every existing case stays clean.

## Design decisions

- `FrictionFactor` throws `ArgumentOutOfRangeException` for a Reynolds number of zero or below, because the equation's `8/Re` and `7/Re` terms are undefined there.
- Relative roughness is not validated, matching the other models.
- The regression case runs at 7.2 L/min through the 2 in PVC pipe so the flow lands in the transitional range, which no other case exercises.

## Tests

`ChurchillFrictionTests` checks:

- the laminar factor against `64/Re` at Re 100, 500 and 1000;
- the transitional factor at Re 3000 against a pinned reference value, within 1 %, so a dropped transition term fails;
- the turbulent factor against `ColebrookFriction` within 1 % over a spread of Reynolds numbers and roughnesses;
- the non-positive Reynolds guard.

## To check by hand

- Run the Drift regression suite and confirm the only difference from the baseline is the new case `water-pvc-2in-transitional-churchill`.
- Confirm that case's Reynolds number falls in the transitional range, since the case name asserts it.

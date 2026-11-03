# Add ChurchillFriction, one friction model valid across every flow regime

`churchill-friction` · Task · enhancement · running · Finalize

The friction models (`SwameeJainFriction`, `HaalandFriction`, `ColebrookFriction`) cover turbulent flow only.

## Expected

- `ChurchillFriction : IFrictionModel` in `src/LymCsharp.Hydraulics` implements Churchill's 1977 equation, valid for laminar, transitional and turbulent flow.
- Tests check it against `64/Re` in laminar flow and against `ColebrookFriction` within 1 % in turbulent flow.
- `RegressionCases` gains one case that runs a pipe flow through `ChurchillFriction`; the existing cases are unchanged.

## Why

This run proves how Drift treats a new regression case that the baseline does not hold, while every existing case stays clean.

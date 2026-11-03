# Add ChurchillFriction, one friction model valid in every flow regime

`LymCsharp.Hydraulics` gains `ChurchillFriction`, an `IFrictionModel` implementing Churchill's 1977 equation.
The existing models switch to 64/Re below Re 2300 and use a turbulent formula above it;
Churchill's equation is one explicit formula that covers laminar, transitional and turbulent flow.
`RegressionCases` gains one case, `water-pvc-2in-transitional-churchill`, and the existing cases are unchanged.

## Design decisions

- The model has no regime branch: the laminar, rough-turbulent and transition terms blend inside the one formula.
  A non-positive Reynolds number throws `ArgumentOutOfRangeException`, matching the sibling models.
- The new regression case reuses the inputs of `water-pvc-2in-transitional` (2-inch PVC, water, 7.2 L/min, Re about 3000), so it exercises the regime Churchill exists for, and its output sits beside the Swamee-Jain case at the same point.
  The baseline does not hold this case yet, so a compare against an existing baseline reports it as new.

## Tests

`ChurchillFrictionTests` covers:

- the laminar factor equal to 64/Re within 1e-9 percent at Re 100, 500 and 1000;
- the non-positive Reynolds guard;
- agreement with `ColebrookFriction` within 1 percent at turbulent points from Re 1e4 to 1e7 and relative roughness 0 to 0.01.

## To check by hand

- The 1 percent bound holds only at the tested points.
  Churchill deviates further from Colebrook near the transition and in rough pipes at low Re: about 1.7 percent at Re 4000 in a smooth pipe, 3 percent at Re 4000 with relative roughness 0.01, and 2.1 percent at Re 1e4 with relative roughness 0.01.
- Near Re 2000 the transition term already lifts the factor about 0.14 percent above 64/Re, so the laminar test stops at Re 1000.

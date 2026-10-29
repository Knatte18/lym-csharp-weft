# Add ColebrookFriction, an iterative Colebrook-White friction model

`LymCsharp.Hydraulics` gains `ColebrookFriction`, an `IFrictionModel` that solves the Colebrook-White equation directly.
It sits beside `HaalandFriction` and `SwameeJainFriction`, which only approximate that equation, and gives callers an implicit reference model for the same interface.
`PipeFlowCalculator`'s default model, `IFrictionModel`, and the `Regression`, `Units` and `Reporting` projects are unchanged.

## Design decisions

- The laminar branch and input guard match the sibling models: below Re 2300 the factor is 64/Re, and a non-positive Reynolds number throws `ArgumentOutOfRangeException`. The three models stay interchangeable behind `IFrictionModel`.
- The turbulent branch iterates a fixed point on `1/sqrt(f)`, starting from the Swamee-Jain estimate.
  Convergence is a relative step of 1e-12 or less; the cap is 50 iterations.
  Both are private constants, since no caller needs to tune them.
- Hitting the cap throws `InvalidOperationException` instead of returning an unconverged factor, so a call is bounded and never returns a wrong value silently.
  A NaN Reynolds number never satisfies the convergence comparison and so reaches this throw; `NonConvergingInputThrows` pins that.
- The relative roughness is trusted as non-negative, as in the sibling models.

## Tests

`ColebrookFrictionTests` covers:

- the laminar factor and the non-positive Reynolds guard;
- the cap-exhaustion throw;
- the Colebrook-White residual below 1e-9 at representative turbulent points;
- agreement with Haaland and with Swamee-Jain within 3 percent over Re 1e4 to 1e7 and relative roughness 0 to 0.01.
  3 percent matches the existing Haaland-versus-Swamee-Jain test.

One `[Category("Slow")]` test, `ConvergesAcrossReynoldsAndRoughnessGrid`, sweeps a logarithmic Re grid from 4000 to 1e8 against relative roughness 0 to 0.05.
It asserts the residual at every point, so a non-converging point fails through the throw and a wrong root fails through the residual.

## To check by hand

- Run the `Slow` category with `lym gate test` and confirm the sweep passes; it is the only check on the extremes of the grid (Re 1e8, roughness 0.05).
- Behavior outside the swept grid, such as relative roughness above 0.05, is untested; it would throw rather than return a wrong factor.

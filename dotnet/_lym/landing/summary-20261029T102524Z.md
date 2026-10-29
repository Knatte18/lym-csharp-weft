# Add a Colebrook-White friction model

Adds `ColebrookFriction`, an `IFrictionModel` in `LymCsharp.Hydraulics` beside `HaalandFriction` and `SwameeJainFriction`.
It solves the implicit Colebrook-White equation instead of approximating it explicitly.

## Behavior

- Below Re 2300 it returns the laminar 64/Re, and a non-positive Reynolds number throws `ArgumentOutOfRangeException`, as the sibling models do.
- Above that it iterates the fixed-point map `x = -2 log10(ε/3.7 + 2.51 x / Re)` on `x = 1/sqrt(f)`, starting from the Swamee-Jain estimate.
  It stops when successive iterates differ by less than a relative 1e-12, which takes a handful of steps over the turbulent range.
- When the iteration has not converged after 50 steps it throws `InvalidOperationException`, so it never loops unbounded and never returns an unconverged factor.

## Design decisions

- The tolerance and iteration cap are private constants; no caller needs to tune them.
- The model is not wired into `PipeFlowCalculator`, whose default stays Swamee-Jain.
- `LymCsharp.Regression`, `LymCsharp.Units` and `LymCsharp.Reporting` are unchanged.

## Tests

`ColebrookFrictionTests` covers:

- the laminar branch and the non-positive-Reynolds guard;
- that turbulent factors satisfy the Colebrook-White equation to a residual below 1e-9;
- agreement with both Haaland and Swamee-Jain within 3 percent over Re 1e4 to 1e7 and relative roughness 0 to 0.01;
- a `[Category("Slow")]` sweep over a logarithmic Re grid from 4000 to 1e8 and relative roughness 0 to 0.05, asserting convergence and the residual at every point.

Both gate tiers pass: tier 0 excludes the `Slow` category, and tier 1 runs only `Slow` tests.

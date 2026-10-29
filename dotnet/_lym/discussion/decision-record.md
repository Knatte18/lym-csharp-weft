# Decision record — friction-colebrook

## Goal

Add `ColebrookFriction` (new), an `IFrictionModel` in `src/LymCsharp.Hydraulics/` that solves the Colebrook-White equation iteratively,
with tests comparing it against the Haaland and Swamee-Jain models and one `Slow` convergence sweep.

## Scope

- `src/LymCsharp.Hydraulics/ColebrookFriction.cs` (new): the model.
- `tests/LymCsharp.Hydraulics.Tests/ColebrookFrictionTests.cs` (new): its tests, including the `Slow` sweep.
- No change to `LymCsharp.Regression`, `LymCsharp.Units`, `LymCsharp.Reporting`, `IFrictionModel` or `PipeFlowCalculator`'s default model.

## Decisions

### D1 — Same laminar branch and input guard as the sibling models

Below Re 2300 the factor is 64/Re, and a non-positive Reynolds number throws `ArgumentOutOfRangeException`.
Rationale: `HaalandFriction` and `SwameeJainFriction` do the same, so the three models are interchangeable behind `IFrictionModel`.

### D2 — Fixed-point iteration on 1/sqrt(f)

In the turbulent branch the model iterates `x = -2 log10(ε/3.7 + 2.51 x / Re)` with `x = 1/sqrt(f)`,
starting from the Swamee-Jain estimate, until successive values differ by less than a fixed relative tolerance of 1e-12,
capped at 50 iterations.
Rationale: the map is a contraction over the turbulent range, so it converges in a handful of steps from a close start;
the tolerance and cap are private constants, since no caller has asked to tune them.

### D3 — Exhausting the iteration cap throws

Reaching the cap without convergence throws `InvalidOperationException`.
Rationale: non-convergence within the cap means a defect in the model, not an input to tolerate;
the bound is the cap itself, so a call never loops unbounded and never returns an unconverged factor.

### D4 — Agreement with the explicit models within 3 percent

Tests compare the Colebrook factor with Haaland and Swamee-Jain over a turbulent grid of Re 1e4 to 1e7 and relative roughness 0 to 0.01, within 3 percent.
Rationale: both explicit approximations are published as within about 2 percent of Colebrook-White over that range, and 3 percent matches the existing Haaland-versus-Swamee-Jain test.

### D5 — One Slow convergence sweep

A `[Category("Slow")]` test sweeps a logarithmic grid of Re from 4000 to 1e8 and relative roughness from 0 to 0.05,
asserting that each factor satisfies the Colebrook-White equation to a residual below 1e-9.
Rationale: a non-converging point throws per D3 and fails the test, and the residual check pins that the returned value solves the equation.

## Constraints

- NUnit 4, the framework `tests/LymCsharp.Hydraulics.Tests/LymCsharp.Hydraulics.Tests.csproj` references.
- Slow tests carry `[Category("Slow")]`, as the README states.

## Auto-mode assumptions

- The relative-roughness argument is trusted as non-negative, as the sibling models trust it.

## Open risks

- A fixed-point step may fail to converge for an extreme roughness outside the swept grid; D3 makes that loud rather than silent.

## Acceptance criteria

- Laminar factor equals 64/Re and a non-positive Reynolds number throws (D1).
- A turbulent factor satisfies the Colebrook-White equation to a residual below 1e-9 (D2).
- Exhausting the iteration cap throws rather than returning a value; covered by the sweep never throwing over its grid (D3).
- Colebrook agrees with Haaland and with Swamee-Jain within 3 percent over the turbulent grid (D4).
- The `Slow` sweep converges at every grid point and passes, and runs only when the `Slow` category is included (D5).
- `lym gate test` passes with and without the `Slow` category.

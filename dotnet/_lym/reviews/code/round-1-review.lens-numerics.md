```yaml
lens: numerics
outcome: reported
```

Task: review the numerics lens (floating-point, units, ranges) of the `Reynolds` extraction in `PipeFlow.cs` and its test.

**Per-item list (case run, result)**

- `MeanVelocityMetresPerSecond`, flow 10 L/s: the old inline code computed `q = flow.ConvertTo(m³/s).Value` and then `q / AreaSquareMetres`. The new helper runs the same two operations in the same order, so velocity is bit-identical. Correct.
- `MeanVelocityMetresPerSecond`, flow 0 or −1: the old code threw `ArgumentOutOfRangeException(nameof(flow), "Flow must be positive.")`. The new helper throws the same exception type, `ParamName` and message, because `nameof(flow)` resolves to the helper's `flow` parameter, which has the same name. Correct.
- `MeanVelocityMetresPerSecond`, flow NaN: `NaN <= 0` is false, so the check passes and NaN propagates through to the result. The old code did the same, and `Reynolds` now inherits it. Not a regression.
- `Reynolds`: the expression is `ρ * v * D / μ`, with `*` and `/` left-associative, which matches the old operand order exactly. IEEE-754 evaluation is deterministic, so the result is bit-identical to the old `reynolds`.
- `Calculate`: velocity is derived twice, once directly and once inside `Reynolds`. Both derivations use identical operations on identical inputs, so they give the same bits. Friction, drop and the `PipeFlowResult` fields therefore stay bit-identical; `drop` still uses the same `velocity * velocity` order.
- `Calculate`, non-positive flow: the exception is thrown at the first `MeanVelocityMetresPerSecond` call before `Reynolds` runs. Same exception as before. Correct.
- `ReynoldsMatchesFourRhoQOverPiMuD`: the closed form `4ρQ/(πμD)` differs from the implementation only by rounding, on the order of a few ulps (about 1e-16 relative). The `1e-12` relative tolerance is comfortably loose and not flaky.
- The test's hard-coded flows, `Expected(Fluid.Oil, 0.0001)` and `Expected(Fluid.Water, 0.01)`, match the 0.1 L/s and 10 L/s inputs, so there is no unit mismatch. The tolerance would absorb a 1e-16 difference between `0.1 * 1e-3` and `0.0001`.
- Unguarded zero or negative `Pipe.DiameterMetres` or `Fluid.ViscosityPascalSeconds` would give Infinity, NaN or a negative Reynolds number. Neither the repository's rules nor the board entry asks for such guards, and the behaviour is unchanged from the old inline code, so I ask for none.

**Findings**

- LOW / design / `src/LymCsharp.Hydraulics/PipeFlow.cs:31-35`: `Reynolds` is now public and accepts physically invalid pipes and fluids (zero diameter or viscosity, negative values) without a guard. The effect is the same as in the old inline path, so it is not a regression, and the repo has no rule requiring guards. I raise it as an observation only; no change is needed to meet the board entry.

No other numerics findings.

**Tool calls:** none. The manifest already held the diff, the full `PipeFlow.cs`, `PipeFlowTests.cs` and `Pipe.cs`.

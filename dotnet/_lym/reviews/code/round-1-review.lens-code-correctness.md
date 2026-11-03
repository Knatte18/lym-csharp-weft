```yaml
lens: code-correctness
outcome: reported
```

Task: code-correctness lens over the `Reynolds` extraction in `PipeFlow.cs`. This is a read-only review from the inherited context, and I made no tool calls.

**List (function or branch, case run, verdict)**

- `MeanVelocityMetresPerSecond`, flow 0 L/s: `q = 0`, so `q <= 0` throws `ArgumentOutOfRangeException` with `ParamName "flow"`. Correct. The name `nameof(flow)` still resolves because the parameter is called `flow` in the new method. This matches the old inline check.
- `MeanVelocityMetresPerSecond`, flow -1 L/s: `q < 0`, same exception. Correct.
- `MeanVelocityMetresPerSecond`, flow 10 L/s: `q = 0.01`, so it returns `0.01 / area`. This is the same expression as the old `q / pipe.AreaSquareMetres`. Correct.
- `MeanVelocityMetresPerSecond`, NaN flow: `NaN <= 0` is false, so it passes the check and returns NaN. The old code behaved the same, so there is no regression. Correct.
- `Reynolds`, oil at 0.1 L/s: the velocity is the same double as before. The expression `rho * v * D / mu` evaluates left to right, exactly as the old inline `fluid.DensityKgPerCubicMetre * velocity * pipe.DiameterMetres / fluid.ViscosityPascalSeconds` did. The result is bit-identical. Correct.
- `Reynolds`, non-positive flow: it throws before any arithmetic, so no negative Reynolds number is returned. Correct.
- `Calculate`, 10 L/s water: it derives `velocity` once and calls `Reynolds`, which derives it again from the same inputs. Both derivations are deterministic IEEE operations on the same operands, so they give the same double. `Reynolds` is therefore bit-identical to the old inline value. `f`, `drop` and `Classify(reynolds)` use the same operands in the same order as before, so every `PipeFlowResult` field is unchanged. Correct.
- `Calculate`, non-positive flow: the first call to `MeanVelocityMetresPerSecond` throws before any state is touched. The method has no side effects, and the injected `friction` model is not called. The exception type, `ParamName` and message are unchanged. Correct.
- `Calculate`, a throwing friction model: the Reynolds step happens before `friction.FrictionFactor`, as before. No state is left inconsistent. Correct.
- Floating-point contraction: .NET may fuse operations with FMA only where explicit, and RyuJIT does not auto-contract `a*b+c`. The extraction adds no new fusion opportunity, since the expressions are unchanged and the only change is a method boundary. Inlining across it cannot reorder IEEE operations. Correct.
- `Classify`: unchanged, and not touched by the diff.

**Findings:** none.

**Tool calls:** none made. The context already held both changed files, the diff and the decision record.

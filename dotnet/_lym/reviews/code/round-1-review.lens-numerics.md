```yaml
lens: numerics
outcome: reported
```

Task: numerics lens review of the cached-friction-factor diff (floating-point keys, precision, units, accepted ranges).

**Per-item list**

- Cache key `(double Reynolds, double RelativeRoughness)` (`CachedFrictionModel.cs:16,29`): exact, no rounding. `ValueTuple<double,double>` equality uses `EqualityComparer<double>.Default`. Equal bit patterns hit, and the cached value is the inner model's output for that exact input, so results stay bit-identical. Correct, as the parent decision requires.
- `+0.0` vs `-0.0` roughness: `0.0.Equals(-0.0)` is true and the hash codes match (.NET Core 3.0+ normalises them). Both map to one entry. The inner models compute `relativeRoughness / 3.7` and get ±0 either way, so the factor is identical. No effect.
- NaN Reynolds: `NaN <= 0` is false, so the guard doesn't throw. `NaN < 2300` is also false, so the call takes the cache path. `Double.Equals(NaN, NaN)` is true, so NaN is a valid, stable key and the NaN result is cached. Every inner model's guard is `reynolds <= 0`, which also lets NaN through. The cached model therefore matches the inner behaviour: a NaN factor, no throw. Not a defect. Reaching it needs NaN to get past `Reynolds(...)`, for example a NaN viscosity ratio. That is outside this diff.
- +Infinity Reynolds: it passes the guard and is cached. The inner `Math.Pow(inf, 0.9)` gives inf, so the term is `log10(rr/3.7)` and the factor is finite or NaN, the same as uncached. No divergence.
- NaN or infinite `relativeRoughness`: it behaves like NaN Reynolds, with a stable key and the same output as the inner model.
- Reynolds boundary at 2300: the inner models use `< 2300` and the wrapper uses `< LaminarLimit` (2300), so the switch point agrees. The inner models are also continuous or discontinuous in the same way as before, because the wrapper only skips the lookup below 2300. `PipeFlowCalculator.Classify` uses the same 2300. A model with a different internal limit (Churchill, Colebrook) is unaffected, because the wrapper only chooses whether to cache.
- Units: the diff changes no unit conversion. `Calculate` still passes `pipe.RoughnessMetres / pipe.DiameterMetres` (dimensionless) and the Reynolds number computed from SI values, as before. No new unit mixing.
- Cast or overflow: there are none. `Calls` is an `int` in a test stub and cannot overflow in these tests.
- Physical range: the wrapper adds no new acceptance. A `Reynolds <= 0` throw is kept. A negative `relativeRoughness` is not rejected, and the inner models don't reject it either, so the wrapper adds no gap.
- Tests, floating-point equality: `Is.EqualTo(CountingFriction.Factor(...))` compares exact doubles. This is valid because the stub's `Factor` is deterministic, and the wrapper returns the value unmodified. The 2299.9 and 2300.0 cases do hit the boundary.
- Cache growth: with exact keys, a sweep of distinct Reynolds numbers never hits and holds one entry per point, and each stored entry costs a `ConcurrentDictionary` node. Memory only; no numeric effect.

**Findings**

1. LOW, class `design`, `src/LymCsharp.Hydraulics/CachedFrictionModel.cs:21-26`: no numerics defect, but a NaN `reynolds` is stored in the cache and returned instead of throwing. This matches the inner models' `<= 0` guard. If the intended contract is "a Reynolds number that is not positive throws" (`CachedFrictionModel.cs:9`), NaN is neither positive nor non-positive, so the doc is imprecise but consistent with the inner models. No change needed; the doc and the guard agree with the inner models. Evidence: `SwameeJainFriction.cs:8`, `ChurchillFriction.cs:12`, `ColebrookFriction.cs:15`, `HaalandFriction.cs:8`.
2. NIT, class `consistency`, `tests/LymCsharp.Hydraulics.Tests/CachedFrictionModelTests.cs`: no row pins NaN or infinite input to the cached path. Adding one is optional. Per the testing rules the NaN behaviour is not a guarded boundary, so I do not ask for it. This is recorded for completeness only.

I found no BLOCKING or MEDIUM numerics defect.

**Tool calls:** none. The inherited context already held the diff, the inner models and `PipeFlow.cs`.

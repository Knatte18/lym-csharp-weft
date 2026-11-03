```yaml
lens: code-correctness
outcome: reported
```

Task: code-correctness lens review of the cached-friction-factor diff (`CachedFrictionModel`, `PipeFlowCalculator` wiring).

**List: function or branch, case run, correct or not**

1. `CachedFrictionModel.FrictionFactor`, guard `reynolds <= 0`:
   - Re = 0 and Re = -5 throw `ArgumentOutOfRangeException` before the inner model is reached. Correct, and it matches every inner model's guard (`SwameeJainFriction.cs:8`, plus the Haaland, Colebrook and Churchill guards at the lines grep showed).
   - Re = NaN: `NaN <= 0` is false, so no throw.
     - `NaN < 2300` is also false, so the call takes the cache path.
     - The inner models' `<= 0` guards also pass NaN. Swamee-Jain returns NaN (`Math.Pow(NaN, 0.9)` is NaN, and so is the log).
     - So the inner model would not have thrown either, and the guard matches it.
     - The cache stores NaN. `ValueTuple<double,double>` equality uses `EqualityComparer<double>.Default`, where `NaN.Equals(NaN)` is true, so the key is found again and returns the same NaN. The result is the same as uncached. Correct.
     - A NaN key is a harmless entry; in practice `Calculate` cannot reach it, because the flow and viscosity are validated or finite.
   - Re = +Infinity: passes the guard and goes to the cache path. The inner model returns 0.25/(log10(r/3.7)²), a finite value, and the cache stores it. Same as uncached. Correct.
   - Relative roughness NaN or negative is not validated, which matches the inner models. Correct.
   - Cache key with `-0.0` and `0.0` roughness: `double.Equals(0.0, -0.0)` is true and the hash codes are equal in .NET Core, so both share one entry. The inner result is identical for either, so it is correct.
2. Branch `reynolds < LaminarLimit` (2299.9, 1000): calls the inner model directly with no caching, so the inner model's `reynolds < 2300` boundary matches. Re = 2300 exactly goes to the cache path, and the inner model treats 2300 as turbulent. Correct.
3. `factors.GetOrAdd((reynolds, relativeRoughness), key => inner...)`:
   - It is called with the exact key, with no rounding, so the result is bit-identical to the inner model (the decision-record amendment). Correct.
   - If the inner model throws inside the factory, `GetOrAdd` adds nothing and the exception propagates, so there is no inconsistent state. Correct.
   - Under concurrency the factory may run more than once, but the inner model is deterministic and pure, so every caller gets the same value. Correct.
   - The lambda captures `inner`, so it allocates a closure on each call. This is a performance nit only; I did not rate it as a finding.
4. `PipeFlowCalculator` field initializer `cachedFriction = new(friction)`:
   - With a primary constructor, the initializer runs for every constructor path. The parameterless constructor chains to `this(new SwameeJainFriction())`, so `friction` is non-null and both constructors are wrapped. Correct.
   - `friction` null: `new CachedFrictionModel(null)` does not throw at construction, and a null `inner` would only fail with a `NullReferenceException` at the first turbulent or laminar call. Before the diff, a null model also failed at the first `Calculate`, so the behavior is unchanged. This is not a finding.
5. `Calculate`:
   - It is the only caller of the friction model. `friction` is now captured by the primary constructor and read only by the field initializer.
   - Flow ≤ 0 throws from `MeanVelocityMetresPerSecond` before the cache is touched. Correct.
   - Result order and values are unchanged. Correct.
   - Each calculator instance has its own cache, so instances do not share state.

**Findings:** none at BLOCKING, MEDIUM or LOW.

One NIT, which I decline to raise as a finding: the closure allocation noted in item 3.

**Tool calls:** none. My context already held every file I needed.

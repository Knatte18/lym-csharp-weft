```yaml
lens: fix-code-correctness
outcome: reported
```

Task: check the fix-code-correctness lens on the F1 test (`RepeatedTurbulentFlowReachesFrictionModelOnce`, `CountingFriction`) and the F2 doc edit.

No findings.

Trace of the F1 test:
- Water at 10 L/s through `SteelPipe` gives Re of about 1.6e5 (the existing `WaterAtModerateFlowIsTurbulent` and `ReynoldsMatchesFourRhoQOverPiMuD` tests classify it turbulent). That is above `LaminarLimit` 2300, so the cache path runs.
- The first `Calculate` call misses the cache and calls `CountingFriction`, so `CallCount` becomes 1. The second call computes the same `reynolds` and `pipe.RoughnessMetres / pipe.DiameterMetres` deterministically, hits the cache, and leaves the count at 1. The assertion `Is.EqualTo(1)` holds.
- With the defect in place (the `cachedFriction` field initializer or its use in `Calculate` replaced by a direct `friction` call), the count would be 2. The test fails, so it pins the wiring the review asked for.
- The `PipeFlowCalculator()` constructor chains to the primary constructor, so one test covers both.
- `CountingFriction` returns 0.02, which is finite and positive, so `Calculate` has no failure path in this test.
- Adding a nested private class and a test changes no signature, so no caller is affected.
- F2 touches only a doc comment. The new wording matches `CachedFrictionModel`'s "2300 and above" behavior.

Tool calls:
1. Read the `CachedFrictionModel` source with `grep -rln "class CachedFrictionModel" src | head -1 | xargs cat`. My context lacked it, and I needed it to check the 2300 threshold and the cache key.

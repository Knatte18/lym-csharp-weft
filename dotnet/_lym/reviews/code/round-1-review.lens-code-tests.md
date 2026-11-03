```yaml
lens: code-tests
outcome: reported
```

Task: code-tests lens. For each behaviour the diff adds, find the test that would fail if it broke.

**Per behaviour, the test that pins it**

| Behaviour | Test that pins it |
|---|---|
| A repeated call at Re ≥ 2300 skips the inner model and returns the same factor | `RepeatedTurbulentCallReturnsCachedFactorWithoutReachingInnerModel`, rows 2300 (the boundary) and 100 000 |
| Exact keys, no rounding: a different Re or a different roughness is a separate entry | `DistinctTurbulentInputsEachReachInnerModelAndKeepTheirOwnFactor`. Re 10 000.5 would collapse into 10 000 under whole-number rounding, so this test would fail if rounding came back. |
| Below 2300 every call reaches the inner model | `LaminarCallAlwaysReachesInnerModel`, rows 1000 and 2299.9 (just under the boundary) |
| Re ≤ 0 throws `ArgumentOutOfRangeException` before the inner model is reached | `NonPositiveReynoldsThrowsWithoutReachingInnerModel`, rows 0 and -5 |
| `PipeFlowCalculator` wraps its model in `CachedFrictionModel` in both constructors, so `Calculate` goes through the cache | None (finding 1) |
| Each calculator instance has its own cache | None (finding 2) |
| Thread safety through `ConcurrentDictionary` | None (finding 2) |
| The `PipeFlow.cs` doc comment | Exempt as static prose |

**Findings**

1. **MEDIUM, design.** Location: `src/LymCsharp.Hydraulics/PipeFlow.cs:18-21` and `:47`.
   - **Summary:** No test pins that `PipeFlowCalculator` routes `Calculate` through `CachedFrictionModel`.
   - **Evidence:**
     - `CachedFrictionModelTests.cs` only tests the decorator in isolation.
     - Results are bit-identical with or without the wrapper, so the existing `PipeFlowTests` and `FrictionTests` pass if `cachedFriction` is dropped from `PipeFlow.cs:47` or the field initializer is removed.
     - The decision record's Change section says both constructors must wrap, and this is the point of the change.
   - **Fix:** Add one test that constructs `PipeFlowCalculator` with a counting stub, calls `Calculate` twice with the same turbulent pipe, fluid and flow, and asserts the stub's call count is 1.
   - **Scope of the gap:** The parameterless constructor chains to the primary constructor, so one test on the primary constructor covers both.

2. **LOW, design.** Location: `src/LymCsharp.Hydraulics/CachedFrictionModel.cs:10-11` and `:15`.
   - **Summary:** No test pins the doc comment's "safe to share across threads" claim or the summary's "each calculator has its own cache" claim.
   - **Evidence:**
     - A per-instance check is cheap: two calculators over one stub each reach the stub once.
     - A deterministic concurrency test is hard to write. `GetOrAdd` may run the factory twice under a race, and the values are identical either way.
   - **Fix:** The per-instance check is optional. I would not require a concurrency test.

**No other findings**
- No new test duplicates another. The two `Repeated…` rows and the two `Laminar…` rows each pin a distinct boundary or regime.
- A NaN Reynolds number is not tested. The `<= 0` guard matches the inner models' own guard, so this is not a behaviour the diff adds.

**Tool calls:** none. My inherited context already held the diff and the inner models.

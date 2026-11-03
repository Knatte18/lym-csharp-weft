```yaml
lens: code-affected-set
outcome: reported
```

Task: the code-affected-set lens, covering what the diff's changes force outside the lines they touch.

**Per change, the set I found:**
- **`PipeFlowCalculator` now wraps its model in `CachedFrictionModel` (both constructors): covered.**
  - The only production caller outside the file is `src/LymCsharp.Regression/RegressionCases.cs:54`. It builds a fresh calculator per case and takes `friction_model` from the unwrapped `friction.GetType().Name`, so the report is unchanged.
  - `PipeFlowTests.cs` calls the calculator at lines 71–157.
  - `CustomFrictionModelIsUsed` (line 146) uses a `ConstantFriction` stub at Re ≥ 2300 and still returns 0.02 through the cache.
  - The static `Classify` and `Reynolds` members don't reach the model.
- **New public type `CachedFrictionModel`: covered.**
  - No other file refers to it.
  - `README.md` has no mention of friction models, caching or `PipeFlow`, so no doc is left stale.
  - The repo has no `PATTERN.md`, no `pattern/` directory and no `CLAUDE.md`. The `CLAUDE.local.md` is workflow-only. There are no declared PATTERN changes to land.
- **`PipeFlowCalculator` doc comment: covered.** The "turbulent" wording matches `CachedFrictionModel`, which caches at Re ≥ 2300 (it also covers transitional flows). That is the handler's wording call, not an affected-set defect.
- **Commit message and departures: covered.** There is one commit and no `Pattern-Departure:` trailer. The message states exact keys and bit-identical results, which matches the amended entry.
- **Mutable state added to a previously stateless `PipeFlowCalculator`: covered.** The cache is a `ConcurrentDictionary`, so existing sharing across threads stays safe. The `Slow` sweep test and the regression runner each use one calculator on one thread.

**Findings:** none.

**Note, not a finding:** `PipeFlowTests.DropGrowsMonotonicallyAcrossFlowSweep` (lines 154–165) never repeats an input, so the cache gives it no speedup. The change's stated motivation is not met for that test. The landing summary admits this and the parent's exact-keys decision causes it, so it is not a defect in the diff.

**Tool calls:**
1. Searched for `CLAUDE.md`, `PATTERN.md`, `README.md` and every `PipeFlowCalculator` reference, because the manifest held no caller list or rule documents.
2. Read `README.md` matches, the `PipeFlowTests.cs` custom-model and sweep tests, the `RegressionCases.cs` caller and `Directory.Build.props`, because the manifest held none of these.

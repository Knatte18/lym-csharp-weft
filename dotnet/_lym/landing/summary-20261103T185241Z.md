# Cache turbulent friction factors in PipeFlowCalculator

`PipeFlowCalculator` now remembers the friction factor of every turbulent or transitional flow it has solved,
so solving the same flow again skips the friction model's `Math.Pow` and `Math.Log10` work.
Every calculated result stays bit-identical to before.

## Change

- New `CachedFrictionModel : IFrictionModel` in `src/LymCsharp.Hydraulics/CachedFrictionModel.cs` wraps an inner model.
  - Below a Reynolds number of 2300 it calls the inner model every time, because 64/Re costs less than a lookup.
  - At 2300 and above it keys the factor on the exact `(Reynolds, RelativeRoughness)` pair and calls the inner model only on a miss.
  - A Reynolds number that is not positive throws `ArgumentOutOfRangeException` before the inner model is reached, so a wrapped model that never validates still rejects it.
- `PipeFlowCalculator` wraps the model it is given in `CachedFrictionModel`.
  The parameterless constructor chains through the same path, so both constructors are covered, and the public API is unchanged.
  Each calculator instance has its own cache.

## Design decisions

- **Exact keys, no rounding.**
  The board entry asked for the Reynolds number to be rounded to the nearest whole number before the lookup.
  The parent decision recorded in the decision record dropped that rounding, because computing the factor at a rounded Reynolds number changes `friction_factor` and `pressure_drop_pa`.
  At 10 significant digits the change shows as drift on every regression case at Re ≥ 2300, which breaks the entry's "results unchanged" invariant.
  The cost is that only an exact repeat of the inputs is a hit:
  a flow sweep of distinct flows, such as `PipeFlowTests.DropGrowsMonotonicallyAcrossFlowSweep`, gets no speedup.
- **`ConcurrentDictionary` instead of `Dictionary`.**
  Before this change `PipeFlowCalculator` held no mutable state, so a caller could share one calculator across threads.
  A plain `Dictionary` would make that sharing unsafe, so the cache uses `ConcurrentDictionary.GetOrAdd`.

## Open risks

- The cache is never evicted.
  A long-lived calculator that solves many distinct turbulent flows holds one entry per flow:
  a 100 000-point sweep holds 100 000 entries until the calculator is collected.

## Tests

- `tests/LymCsharp.Hydraulics.Tests/CachedFrictionModelTests.cs` uses a call-counting stub model:
  - a repeated call at Re 2300 and at 100 000 reaches the inner model once and returns its value;
  - inputs that differ in either the Reynolds number or the roughness each reach the inner model and keep their own factor;
  - a laminar call reaches the inner model every time;
  - a Reynolds number that is not positive throws and never reaches the inner model.
- `PipeFlowTests` and `FrictionTests` are unchanged.

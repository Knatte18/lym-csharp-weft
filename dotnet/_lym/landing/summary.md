# Cache friction factors in PipeFlowCalculator

`PipeFlowCalculator` no longer recomputes the friction factor for a flow it has already solved.
A flow sweep such as `PipeFlowTests.DropGrowsMonotonicallyAcrossFlowSweep` calls `IFrictionModel.FrictionFactor` once per point,
and the turbulent models spend most of their time in `Math.Pow` and `Math.Log10`.
This is a performance change only: every calculated result is unchanged.

## What changes

- New `CachedFrictionModel`, an `IFrictionModel` decorator over an inner model.
  - Below a Reynolds number of 2300 it calls the inner model directly, since the laminar factor is cheaper than a lookup.
  - At 2300 and above it returns a stored factor for an exact repeat of the Reynolds number and relative roughness, and otherwise asks the inner model and stores the answer.
  - A Reynolds number that is not positive throws `ArgumentOutOfRangeException`, before the inner model is reached.
- `PipeFlowCalculator` builds one `CachedFrictionModel` over the model it is given and calls it from `Calculate`.
  Both constructors get the cache, and the public API is unchanged.

## Design decisions

- The cache keys on the exact Reynolds number and relative roughness, with no rounding.
  Rounding the Reynolds number would change `FrictionFactor` and `PressureDropPascals` for every case at 2300 and above, which contradicts the unchanged-results goal.
  The cost is that a hit needs an exact repeat of the inputs.
- The cache is a `ConcurrentDictionary`, so a shared calculator stays thread-safe.
  Under a race the inner model may run more than once for a new key; every run returns the same value, so only one is kept.
- Entries are never evicted, so memory grows by one entry per distinct turbulent input.
  The cache lives as long as its calculator.

## Tests

- `CachedFrictionModelTests` covers a repeat turbulent call that does not reach the inner model (at 2300 and at 100 000), distinct inputs that each reach it, laminar calls that always reach it, and non-positive Reynolds numbers that throw.
- `PipeFlowTests.RepeatedTurbulentFlowReachesFrictionModelOnce` pins that `Calculate` reaches the friction model through the cache.

## To check by hand

- `PipeFlowCalculator.Calculate` must call `cachedFriction`, not the `friction` constructor parameter.
  A direct call would pass every existing result test and only lose the speedup, so confirm `RepeatedTurbulentFlowReachesFrictionModelOnce` is the test that guards it.
- The speedup itself was not measured.
  Time the `Slow` sweep test before and after if the size of the gain matters.

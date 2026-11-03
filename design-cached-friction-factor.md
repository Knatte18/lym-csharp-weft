# Cache friction factors in PipeFlowCalculator

`cached-friction-factor` · Task · enhancement · running · Code-Burler-Fix

## Goal

Stop `PipeFlowCalculator` from recomputing the friction factor for flows it has already solved.
A flow sweep, such as the `Slow` test `PipeFlowTests.DropGrowsMonotonicallyAcrossFlowSweep`, calls `IFrictionModel.FrictionFactor` once per point,
and the turbulent models spend most of their time in `Math.Pow` and `Math.Log10`.

This is a performance change only: every calculated result stays unchanged.

## Change

- Add `CachedFrictionModel : IFrictionModel` in `dotnet/src/LymCsharp.Hydraulics/CachedFrictionModel.cs`, a decorator over an inner `IFrictionModel`.
  - Below a Reynolds number of 2300 it calls the inner model directly; the laminar factor 64/Re is cheaper than a lookup.
  - At 2300 and above it rounds the Reynolds number to the nearest whole number and keys a `Dictionary<(double Reynolds, double RelativeRoughness), double>` on the rounded Reynolds number and the relative roughness.
    On a miss it computes the inner model's factor at the rounded Reynolds number and stores it, so an entry never depends on which caller filled it first.
  - It validates its arguments as the inner model does: a Reynolds number that is not positive throws `ArgumentOutOfRangeException`.
- `PipeFlowCalculator` wraps the model it is given in `CachedFrictionModel`, in both constructors, so every caller gets the cache without a code change.
- Keep `PipeFlowCalculator`'s public API as it is.

## Tests

- Add `dotnet/tests/LymCsharp.Hydraulics.Tests/CachedFrictionModelTests.cs`:
  - a second call with the same arguments does not reach the inner model (count calls on a stub model);
  - a laminar call always reaches the inner model;
  - a Reynolds number that is not positive throws.
- The existing `PipeFlowTests` and `FrictionTests` stay green unchanged.

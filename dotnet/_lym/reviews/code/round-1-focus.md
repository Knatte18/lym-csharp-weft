---
round: 1
focus:
  - "dotnet/src/LymCsharp.Hydraulics/CachedFrictionModel.cs: does the cache key on the exact Reynolds number and relative roughness with no rounding, as the parent decision (2026-11-03) amended the board entry, so every result stays bit-identical?"
  - "CachedFrictionModel.FrictionFactor: does the `reynolds <= 0` guard match the inner models' validation for every input they reject, including NaN and infinity, or can a NaN key reach the cache and be stored or returned where the inner model would have thrown?"
  - "dotnet/src/LymCsharp.Hydraulics/PipeFlow.cs: does the `cachedFriction` field initializer wrap the model in both constructors, and is `PipeFlowCalculator.Calculate` the only path that reaches the friction model, with the public API unchanged?"
  - "dotnet/src/LymCsharp.Hydraulics/PipeFlow.cs doc comment and CachedFrictionModel doc comment: do they follow the repository's C# XML-doc conventions and state accurately what is cached (turbulent and transitional at Re >= 2300 only)?"
  - "dotnet/tests/LymCsharp.Hydraulics.Tests/CachedFrictionModelTests.cs: do the tests cover each behavior the board entry lists (cache hit skips inner model, laminar always reaches inner, non-positive Re throws), and does any added test duplicate another's coverage?"
  - "_lym/landing/summary.md claims: does the diff bear out the claims of exact keys, per-instance cache, thread safety via ConcurrentDictionary, and both constructors covered?"
---

The measuring stick is the board entry in the decision record as amended by the parent's "no Reynolds rounding" decision.
The change is a single commit with no `Pattern-Departure:` trailer, and the repository has no plan directory.

```yaml
lens: fix-consistency
outcome: reported
```

Task: check the F1/F2 change set for agreement with sibling text and twins.

No findings.

- The `PipeFlowCalculator` doc ("Reynolds number of 2300 or more") now matches `CachedFrictionModel.cs:6-8` ("2300 and above") and its `LaminarLimit = 2300`.
- `CachedFrictionModel.cs:7` still says "the turbulent formula". That is the inner model's formula, which is not what F2 flagged, and the fix did not touch it.
- No other cache or "remembers" text appears in `src` or the docs.
- The new test `RepeatedTurbulentFlowReachesFrictionModelOnce` sits beside the existing `CustomFrictionModelIsUsed`. Both use the same 10 L/s water flow, so they don't contradict each other.
- `CountingFriction` follows the style of the sibling `ConstantFriction` stub.

Tool calls:
1. `grep` for cache, 2300 and turbulent wording in `src`, README and docs, to find sibling statements of the cached range. My context held only `PipeFlow.cs` and `PipeFlowTests.cs`, not `CachedFrictionModel.cs`.

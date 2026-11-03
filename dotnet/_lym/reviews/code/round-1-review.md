---
verdict: APPROVED
findings:
  - id: F1
    severity: MEDIUM
    class: design
    origin: lens:code-tests
    location: tests/LymCsharp.Hydraulics.Tests/PipeFlowTests.cs
    summary: No test pins that PipeFlowCalculator.Calculate reaches the friction model through CachedFrictionModel
    fix: In tests/LymCsharp.Hydraulics.Tests/PipeFlowTests.cs add one test that builds new PipeFlowCalculator(stub) with a call-counting IFrictionModel stub, calls Calculate twice with the same SteelPipe, Fluid.Water and a 10 L/s flow (Re above 2300), and asserts the stub's call count is 1. The parameterless constructor chains to the primary one, so this covers both constructors.
  - id: F2
    severity: LOW
    class: consistency
    origin: handler
    location: src/LymCsharp.Hydraulics/PipeFlow.cs:15
    summary: PipeFlowCalculator doc says it remembers the turbulent friction factor, but the cache also holds transitional flows at Re 2300 to 4000
    fix: In the PipeFlowCalculator doc comment in src/LymCsharp.Hydraulics/PipeFlow.cs replace "the turbulent friction factor of every flow it has solved" with wording that covers Re of 2300 and above, such as "the friction factor of every flow it has solved at a Reynolds number of 2300 or more".
---

### [MEDIUM] No test pins the calculator's use of the cache

**Location:** tests/LymCsharp.Hydraulics.Tests/PipeFlowTests.cs
**Issue:** `CachedFrictionModelTests` tests the decorator alone.
Results are bit-identical with or without the wrapper, so every existing `PipeFlowTests` and `FrictionTests` case still passes if the `cachedFriction` field initializer or its use in `Calculate` is removed.
The board entry's central requirement, that `PipeFlowCalculator` wraps the model it is given, has no row that would fail if it broke.

### [LOW] PipeFlowCalculator doc names only turbulent flow

**Location:** src/LymCsharp.Hydraulics/PipeFlow.cs:15
**Issue:** The cache starts at Re 2300, and `Classify` calls 2300 to 4000 transitional.
The doc comment's "turbulent friction factor" is narrower than what is cached, while `CachedFrictionModel`'s own doc and the landing summary say "2300 and above" and "turbulent or transitional".

## Rejected

- Code-tests F2, no test for per-instance caches or thread safety: per-instance state follows from the field initializer, which F1's test already exercises, and a deterministic concurrency test is impractical because `GetOrAdd` may run the factory twice with identical values.
- Numerics F1, NaN Reynolds is cached rather than rejected: the inner models' `reynolds <= 0` guards also pass NaN and return NaN, so the cached model matches them, and the decision record asks for the inner model's validation.
- Numerics F2, no NaN or infinity row: the behavior is inherited from the inner models and adds no new guarded boundary.
- Code-correctness NIT, closure allocation per call in `GetOrAdd`: a performance micro-cost the lens itself declined to raise, and the change is a performance one only against the inner model's `Math.Pow` and `Math.Log10` cost.
- `ConcurrentDictionary` where the board entry names `Dictionary`: the landing summary states the reason, that `PipeFlowCalculator` was stateless and shareable across threads before this change, and the choice keeps results unchanged.
- Flow sweep gets no speedup under exact keys: the parent's recorded no-rounding decision causes this and the landing summary states it, so it is not a defect of the diff.

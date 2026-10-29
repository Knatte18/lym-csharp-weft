```yaml
lens: fix-consistency
outcome: reported
```

Task: check the F1 and F2 change set in `ColebrookFrictionTests.cs` for agreement with its surroundings (fix-consistency lens).

No findings.

- **F1 sweep:** `steps = 440` keeps the original grid density, since the old span was about 439.8 steps. The first point is exactly Re 4000 and the last is exactly Re 1e8, as D5 states. The only other `Slow` test, in `PipeFlowTests.cs:122`, is not a sibling sweep. No other sweep loop shares the floating-point-accumulator pattern.
- **F2 test:** `NonConvergingInputThrows` is the only `InvalidOperationException` assertion in the Hydraulics tests. The two sibling model test classes have no cap-exhaustion twin, because only `ColebrookFriction` iterates. The test matches the throw documented in the `ColebrookFriction` class summary and in `ColebrookFriction.cs:38`.
- **Test style:** The new test follows the same NUnit `[Test]` and `Assert.Throws` style as `NonPositiveReynoldsThrows`.

Tool calls made:
1. `grep` for `Slow`, `exponent` and `Throws<InvalidOperation` in the other Hydraulics test files, because my context held only `ColebrookFrictionTests.cs` and not its siblings.

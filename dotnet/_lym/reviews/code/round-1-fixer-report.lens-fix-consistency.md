```yaml
lens: fix-consistency
outcome: reported
```

Task: check the fix-consistency lens on the F1 change, the new `ReynoldsRejectsNonPositiveFlow` test in `PipeFlowTests.cs`.

No findings.

- **Behavior:** The test matches the `Reynolds` doc comment at `PipeFlow.cs:29`. `Reynolds` calls `MeanVelocityMetresPerSecond`, which throws `ArgumentOutOfRangeException` when `q <= 0`. The test would fail if that guard were removed.
- **Placement:** It sits directly after `NonPositiveFlowThrows` and uses the same `[TestCase(0.0)]` and `[TestCase(-1.0)]` rows, `Assert.Throws` form and `LitrePerSecond` quantity.
- **Sibling tests:** `NonPositiveFlowThrows` still covers `Calculate`, and `ReynoldsMatchesFourRhoQOverPiMuD` is untouched.
- **Naming:** The name follows the file's behavior-sentence convention.

Tool calls:
1. `grep` in `src/LymCsharp.Hydraulics/PipeFlowCalculator.cs`: failed, because that path doesn't exist.
2. `grep -rn` over `src/LymCsharp.Hydraulics`: showed the `Reynolds` doc comment and guard, which my context lacked because the source was never read.

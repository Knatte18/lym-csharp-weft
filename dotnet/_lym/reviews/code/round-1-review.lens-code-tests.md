```yaml
lens: code-tests
outcome: reported
```

Task: code-tests lens. List per behaviour, the test that pins it, or none.

**Per-behaviour list**
- Laminar factor is 64/Re below 2300: pinned by `LaminarFactorIs64OverRe` (Re 500, 1000, 2000). The 2300 boundary has no row; see F4.
- Non-positive Re throws `ArgumentOutOfRangeException`: pinned by `NonPositiveReynoldsThrows` (0 and -5).
- Turbulent factor solves Colebrook-White to a residual below 1e-9: pinned by `TurbulentFactorSolvesColebrookWhite` and by the `Slow` sweep `ConvergesAcrossReynoldsAndRoughnessGrid`.
- Exhausting `MaxIterations` throws `InvalidOperationException`: none. The decision record says the sweep covers it by never throwing. Deleting the throw, or returning the last iterate, would leave every test green.
- Agreement with Haaland and Swamee-Jain within 3 percent: pinned by `TurbulentFactorAgreesWithExplicitModelsWithinThreePercent`. It checks four diagonal points, not the Re 1e4 to 1e7 by roughness 0 to 0.01 grid, which is thin but real coverage. I hand-checked the (1e4, 0) and (1e7, 0.01) rows and they pass.
- Sweep covers Re 4000 to 1e8 and roughness 0 to 0.05: partial; see F1.
- `Slow` category runs only when included: pinned by the `[Category("Slow")]` attribute alone, which is all it can be.
- Swamee-Jain starting estimate: not observable at the public surface, so no row is needed.

**Findings**

- **F1, LOW, scope.** `ColebrookFrictionTests.cs`, in `ConvergesAcrossReynoldsAndRoughnessGrid`, the `for (var exponent = Math.Log10(4000); exponent <= 8.0; exponent += 0.01)` loop. The sweep never reaches Re 1e8 as decision D5 requires.
  - The loop starts at log10(4000) ≈ 3.602 and steps 0.01. Its last exponent is ≈ 7.992, so the largest Re tested is ≈ 9.8e7.
  - Even on a grid that lands on 8.0, the accumulated `+=` error could drop the endpoint.
  - Fix: use an integer loop counter and compute the exponent as `lo + i * (hi - lo) / steps`, so the last point is exactly 1e8.
  - Evidence: `ColebrookFrictionTests.cs`, the loop line in the diff.

- **F2, LOW, scope.** `ColebrookFriction.cs`, the `throw new InvalidOperationException(...)` after the loop. No test pins the cap-exhaustion throw, so this D3 behaviour has no row that would fail if it broke.
  - A public-surface input that reaches it is `reynolds = double.NaN`: `Log10(NaN)` gives NaN, the tolerance test is false, and the loop runs out.
  - Adding that row asserts `Assert.Throws<InvalidOperationException>` and so pins D3 directly.
  - The record's acceptance criterion accepts the sweep as coverage, which is why this is LOW and not MEDIUM.
  - Evidence: no `InvalidOperationException` assertion in `ColebrookFrictionTests.cs`.

- **F3, NIT, consistency.** `ColebrookFrictionTests.cs`, `TurbulentFactorSolvesColebrookWhite`. It repeats the residual assertion the `Slow` sweep makes.
  - Three of its four rows sit inside the sweep's range: (4000, 0), (1e5, 0), (1e5, 0.001) and (1e7, 0.05) all fall within Re 4000 to 1e8 and roughness 0 to 0.05.
  - The rows are the only residual coverage in the default tier, because tier 0 excludes `Slow`. I judge this defensible and would keep it.
  - If the sweep were the single owner, the test would be redundant. I recommend no change.

- **F4, NIT, scope.** The laminar/turbulent switch at Re 2300 has no row.
  - A change from `< 2300` to `<= 2300` would pass every test.
  - The sibling models' tests have the same gap, so this is parity with them.
  - Fix: add `[TestCase(2300.0)]` to a test asserting the turbulent result, or drop the finding for parity.

**Tool calls**
None. My context already held the diff, the sibling tests and the decision record.

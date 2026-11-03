```yaml
lens: numerics
outcome: reported
```

Task: numerics lens review of `ChurchillFriction`, its regression case and its tests. I made no tool calls and ran nothing, so the arithmetic below is by hand.

**Per function or branch, with the case I ran**
- **Formula terms, any Re > 0:** correct, matching Churchill 1977.
  - `laminarTerm` = (8/Re)^12.
  - `roughTurbulentTerm` is A = [2.457·ln(1/((7/Re)^0.9 + 0.27ε))]^16, using the natural log.
  - `transitionTerm` is B = (37530/Re)^16.
  - The result 8·[(8/Re)^12 + (A+B)^-1.5]^(1/12) is also correct.
  - ε is the dimensionless ε/D, which `PipeFlowCalculator.Calculate` passes (`PipeFlow.cs:41`), so there is no unit mix.
- **Laminar branch, Re = 1000, ε = 0.001:**
  - The rough/transition term is about 1e-38 against a laminar term of 7e-26.
  - f deviates from 64/Re by about 2e-14 relative, against the test tolerance of `Within(1e-9).Percent` = 1e-11 relative.
  - Re = 100 and 500 deviate by less.
  - The test passes with margin. Round-off from the pow^12 then pow^(1/12) chain is a few ulp, well inside the tolerance.
- **Turbulent rows, hand-computed:**
  - Re = 1e4, ε = 0: Churchill ≈ 0.0310, Colebrook ≈ 0.0309, about 0.5 % apart.
  - Re = 1e5, ε = 1e-3: about 0.7 % apart.
  - Re = 1e7, ε = 0.01: about 0.05 % apart.
  - All three are inside the 1 % tolerance.
  - I did not hand-check the other two rows (Re = 1e5 with ε = 1e-4, and Re = 1e6 with ε = 1e-3).
- **Regression case:** Re ≈ 3000 for 7.2 L/min in a 2-inch pipe with water at about 1e-3 Pa·s. That is transitional, so the case name holds, and the formula has no discontinuity there.
- **Overflow and underflow:**
  - Re ≥ about 1e-20: B overflows to +inf and (inf)^-1.5 = 0, so the laminar branch still gives 64/Re.
  - Re ≲ 1e-26: laminarTerm overflows to inf and f = inf.
  - Neither range is physical, so I ask for no guard.

**Findings**
1. **LOW, design, `ChurchillFriction.cs:12-16`:** the `reynolds <= 0` guard lets `double.NaN` through, because the comparison is false for NaN, and `FrictionFactor` returns NaN silently.
   - `ColebrookFriction` handles NaN by throwing `InvalidOperationException` (`ColebrookFrictionTests.cs:25-28`).
   - Here a NaN Reynolds number becomes a NaN pressure drop with no error.
   - Evidence: `ChurchillFriction.cs:12-16`.
   - Whether to guard is the repository's call. `code-quality` says trust internal callers, and `Reynolds` is computed internally from a validated flow. Downgrade to NIT if you apply that rule.
2. **NIT, consistency, `ChurchillFriction.cs`:** `relativeRoughness` is not validated like `reynolds`.
   - A negative value makes the log argument non-positive or produces NaN silently.
   - `ColebrookFriction.cs` and `Pipe` do no better, and the roughness comes from `Pipe`, so I ask for no guard.
   - The focus question has a direct answer: it is not validated, and that matches the sibling models.
3. **NIT, design, `RegressionCases.cs:46-47`:** the baseline records full-precision doubles with `:R` that come from `Math.Pow` and `Math.Log`.
   - The Churchill case chains 12th and 16th powers, so a last-ulp difference across runtimes or platforms would show up as drift.
   - The existing cases have the same exposure, so this adds no new class of risk and is emphasis only.

I have no other numerics findings. There are no float `==` comparisons, no casts, and no unit conversions in the diff.

Tool calls: none, because the context already held every file the lens needed.

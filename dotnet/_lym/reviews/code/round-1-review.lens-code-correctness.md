```yaml
lens: code-correctness
outcome: reported
```

**Lens: code-correctness. Cases run, one per branch and row.**

1. `ResultFormatter.Format`, transitional branch.
   - Input: `PipeFlowResult(0.05, 3000, Transitional, 0.02, 689.475729)` with `psi`.
   - `drop` is 689.475729 / 6894.757293168 ≈ 0.1000000, which `FormatToSignificantDigits` prints as `0.1`.
   - `0.05` prints as `0.05`, because the exponent is -2, so 5 decimals and format `0.#####`.
   - `3000` prints as `3000`, because the exponent is 3, so 0 decimals and format `0.`.
   - `line` is `Transitional flow, v = 0.05 m/s, Re = 3000, dP = 0.1 psi`.
   - `Regime == Transitional` appends `" (transitional: friction factor uncertain)"`, so the result is exactly the board entry's example. Correct.
   - The pressure drop sits at a rounding boundary: the value is either just below or just above 0.1, and the exponent flips between -2 and -1. Both paths round to `0.1`. Correct.

2. `Format`, non-transitional branch.
   - Laminar: `(0.0, 0.0, Laminar, 0.0, "Pa")` gives `line` unchanged, `Laminar flow, v = 0 m/s, Re = 0, dP = 0 Pa`.
   - Turbulent: `(1.5, 50000, Turbulent, 200000, "Pa")` gives `line` unchanged.
   - The `: line` arm returns the same string the old code built, so those lines are byte-identical. Correct.

3. Regime boundaries.
   - `PipeFlowCalculator.Classify` returns Transitional for Re in [2300, 4000), so the note is shown exactly where the board entry says (`PipeFlow.cs:26-31`).
   - `Format` branches on `result.Regime` and does not re-derive it from Reynolds. At Re 2300 and 3999.99 the result's regime is Transitional, so the note appears. At Re 2299.99 and 4000 it does not. Correct.

4. Operator precedence in the changed return.
   - `result.Regime == FlowRegime.Transitional ? line + "…" : line` parses as `(Regime == Transitional) ? (line + note) : line`, since `==` binds tighter than `?:`, and `+` binds tighter than `?:`. Correct.

5. Error paths.
   - `Format` has no new call that can fail. `ConvertTo` and `UnitRegistry.Find` are unchanged and behave as before.
   - A null `result` throws a `NullReferenceException` at `result.PressureDropPascals`, as it did before. The diff leaves it unchanged and adds no state, so nothing is left inconsistent.

6. Regression harness consumer.
   - `RegressionCases.cs:68` calls `Format` for the two transitional cases, which are at Re ≈ 3000 and use `Pvc2Inch`, water and 7.2 L/min.
   - Only those `report =` lines gain the note. The scrubber and harness are untouched. No baseline files are checked in, so nothing else in the repository is affected.
   - Correct.

**Findings:** none.

**Tool calls (1):** `grep` of `UnitCatalog.cs` for the psi factor. My context lacked `UnitCatalog.cs`, and I needed the factor to confirm that 689.475729 Pa gives 0.1 psi.

```yaml
lens: code-tests
outcome: reported
```

Task: the code-tests lens. For each behaviour the diff adds or changes, I name the test row that pins it.

**Per behaviour, the test that pins it**
1. **A transitional flow's line ends with ` (transitional: friction factor uncertain)`.**
   - Pinned by the new row at `tests/LymCsharp.Hydraulics.Tests/ResultFormatterTests.cs:25-26`.
   - It asserts exact equality with `Is.EqualTo(expected)` (line 35), so a dropped note, altered text or wrong spacing fails it.
   - The row is the board entry's example line: 0.05 m/s, Re 3000, 689.475729 Pa converted to psi gives `0.1 psi`.
2. **Laminar and turbulent lines stay byte-identical with no note.**
   - Pinned by the unchanged rows at `ResultFormatterTests.cs:9-24`, which cover both regimes with exact equality.
   - A note appended for any regime other than transitional would fail them.
3. **The note is keyed on `result.Regime == FlowRegime.Transitional`, not on Reynolds.**
   - Pinned by the same rows.
   - Row 25 uses `Transitional` regime with Re 3000.
   - Laminar and turbulent rows supply the other two `FlowRegime` values.
   - Every enum value has a row, so no gap.
4. **The two transitional regression cases' `report =` line gains the note.**
   - No test asserts it. This is the only behaviour with no pinning row.
   - `BaselineHarnessTests.cs:55-77` runs `RegressionCases.All` through capture and compare.
   - It builds baselines at test time, because no baseline files are checked in. The change is therefore not asserted anywhere.
   - I do not raise this as a finding. It is the same behaviour as 1, reached through `ResultFormatter.Format`, which row 25 already covers. Adding a regression assertion would duplicate that coverage. The rubric's test-economy rule flags a duplicate test, not this absence.

**Findings:** none.

**Tool calls**
1. `grep` over `tests/LymCsharp.Regression.Tests` for `RegressionCases` and `Transitional`. My context lacked the regression tests, and I needed to know whether any of them assert case output or a transitional line. The result was huge because it also matched `obj/`, and only the `BaselineHarnessTests.cs` lines were relevant. They show `RegressionCases.All` is used for capture and compare, with no assertion on transitional text.

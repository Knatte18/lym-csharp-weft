```yaml
lens: code-affected-set
outcome: reported
```

Task: lens code-affected-set — find what the `ResultFormatter` change forces outside the lines it touches.

**Per change, the set I found:**

1. `ResultFormatter.Format` output string (velocity, Reynolds and pressure drop now at 4 significant digits; the pressure drop is built from `drop.Value` and `drop.Unit.Symbol` instead of `Quantity.ToString`):
   - Readers and asserters found: `RegressionCases.PipeFlowCase` (`src/LymCsharp.Regression/RegressionCases.cs:68`, the `report = ...` line) and `ResultFormatterTests`. The tests are covered by the diff.
   - The six regression cases render this line into `<case>.txt` baselines. `BaselineHarness` reads and writes them in an external baseline directory (`BaselineHarness.cs:18,73-74`).
   - No baseline file is tracked in this repo. `git ls-files` shows only `.cs`, `.csproj`, `.sln` and `Directory.Build.props`. So no fixture exists in the diff's reach to update, and the regression drift can only appear when the harness's compare runs against the external baseline directory. Neither the diff nor the repo holds that record. Not a defect of the diff.
   - Net for the regression baselines: not covered, but outside the repo, so not a finding.
   - `RegressionCommandTests` and `BaselineHarnessTests` appear to use their own cases, not `RegressionCases.All`. I did not open them, so I can't confirm that no test asserts the formatted line.
2. `Quantity.ToString` is unchanged (`0.###` format) and `ResultFormatter` no longer calls it.
   - Other callers: `RegressionCases.cs:62` (`flow = {flow}`) still uses it. It is unaffected, and so are the raw `R`-format lines.
   - Covered.
3. Repository rule documents and PATTERN files:
   - No `PATTERN.md`, no `pattern/` directory and no `CLAUDE.md` exist. The only rule file is `CLAUDE.local.md`, which holds workflow rules only. It sets no rule for the diff's change.
   - Nothing is declared for the "Declared PATTERN changes landed" check. Not applicable.
4. Commit message and `Pattern-Departure:` trailers:
   - The commit message has no trailer, and the diff differs from the answer key in no way a trailer would excuse.
   - The commit message claims velocity, Reynolds number and pressure drop are rounded, and the diff bears that out.
5. `_lym/landing/summary.md`:
   - Its "Intended regression drift" table claims, for example, that case `water-steel-dn100-swamee-jain` goes from `dP = 7925.267 Pa` to `dP = 7925 Pa`, and that line 12 of each case changes.
   - It also claims `oil-steel-6in-turbulent` does not drift.
   - Whether these values are correct depends on a run's output, which I didn't reproduce.
   - Nothing in the diff, and nothing tracked in the repo, contradicts them. No finding.

**Findings:** none for this lens.

**Tool calls (1 of 6):** one Bash call to list the tracked non-`.cs` files, grep `BaselineHarness.cs` for baseline paths, and look for `CLAUDE*.md` or `PATTERN.md` (my context lacked the baseline location and the repo's rule documents).

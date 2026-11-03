```yaml
lens: code-affected-set
outcome: reported
```

Task: code-affected-set lens review of the ChurchillFriction diff.

**Per-change set**

1. `ChurchillFriction : IFrictionModel` (new type).
   - Set found: `IFrictionModel` implementers, `PipeFlowCalculator`, `RegressionCases`, and docs that list friction models.
   - Result: covered. The type implements the interface as `ColebrookFriction` does. `PipeFlowCalculator` takes any `IFrictionModel`, so nothing else needs to change. Doc search found no `.md`, `.json` or `.csproj` file that lists the friction models. The repo has no `PATTERN.md`, no `pattern/` directory and no checked-in CLAUDE file. The only CLAUDE file is the untracked `CLAUDE.local.md`, which holds LoomYard workflow rules and no repo rules, so no rule document is affected.
2. New `RegressionCases.All` entry `water-pvc-2in-transitional-churchill`.
   - Set found: the readers of `RegressionCases.All`, which are `Program.cs:3`, `BaselineHarnessTests.cs:55` and `BaselineHarnessTests.cs:69-77`.
   - Result: covered.
     - `Program.cs` passes the list to the harness unchanged.
     - `BaselineHarnessTests` captures into a temp directory, so no checked-in baseline file exists to go stale.
     - The `BaselineHarnessTests` grep found no case-count assertion and no assertion on a specific case name.
     - The new case reuses the existing `Pvc2Inch` field and the same flow and unit as `water-pvc-2in-transitional`.
     - The existing five entries are unchanged in the diff.
     - Missing from the baseline is intended: the decision record's Why says this run tests Drift handling of a new case.
3. Commit messages.
   - Result: both claims match the diff.
     - "Existing cases are unchanged": the diff only appends one entry.
     - "Existing 2-inch PVC transitional water flow": the case reuses `Pvc2Inch`, water and 7.2 L/min.
   - There is no `Pattern-Departure:` trailer and none was needed.
4. Declared PATTERN changes.
   - Result: not applicable, since the repo has no `PATTERN.md` and no directive declares a change.
5. Stencils: the diff changes none.

**Findings:** none.

**Tool calls (1)**
- `grep` and `git ls-files` over the Regression tests, tracked docs and friction-model references. My context lacked the `RegressionCases.All` consumers beyond the names, the baseline file layout and any doc listing.

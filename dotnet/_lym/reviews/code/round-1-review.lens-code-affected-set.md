```yaml
lens: code-affected-set
outcome: reported
```

Task: code-affected-set lens for the diff `7f50ffe..HEAD` (extract `Reynolds` from `Calculate`).

**Per change, the set found**

- **`Calculate` behavior (the regression harness's reader).**
  - The only non-test caller is `RegressionCases.cs:51`, which builds a `PipeFlowCalculator(friction)` and calls `Calculate`. The signature is unchanged, so it compiles and behaves as before.
  - Existing tests that assert `Calculate` outputs stay valid: `WaterAtModerateFlowIsTurbulent`, `OilAtLowFlowIsLaminar`, `LaminarDropMatchesHagenPoiseuille`, `FlowUnitDoesNotChangeResult`, `HigherFlowGivesHigherDrop`, `NonPositiveFlowThrows`, `CustomFrictionModelIsUsed`, `DropGrowsMonotonicallyAcrossFlowSweep`. `ResultFormatter` and `ResultFormatterTests` read `PipeFlowResult` fields, and the record is unchanged. Covered.
  - Flow-to-velocity conversion, the non-positive check and the `ArgumentOutOfRangeException` with `ParamName` `flow` all moved into `MeanVelocityMetresPerSecond`. `Calculate` still reaches the throw first, through its own call. Covered by `NonPositiveFlowThrows`.
- **New public `Reynolds`.**
  - There is no other caller. A grep for `Reynolds` finds only `PipeFlowResult.Reynolds`, the friction models' parameter names, the tests and `ResultFormatter`, none of which refer to the new method.
  - `PipeFlowResult` has a property named `Reynolds`, and the calculator now has a method named `Reynolds`. They are on different types, so there is no clash.
  - The new test `ReynoldsMatchesFourRhoQOverPiMuD` covers the laminar and turbulent cases the fasit asks for.
- **Repository rule documents and docs.**
  - The repo has no `PATTERN.md`, `pattern/`, `CLAUDE.md` or changelog. `CLAUDE.local.md` holds only loomyard session workflow rules and nothing about C# docs or changelogs.
  - `README.md` lists libraries and the harness, with no API-level docs, so a new public method needs no README change.
  - Nothing is declared for PATTERN changes, and no `Pattern-Departure:` trailer is needed. The single commit message is a plain one-line title.
- **Summary claims against the diff** (not a lens finding). The summary's "same operands in the same order" claim matches the code, and the double conversion it admits to is visible in the diff.

**Findings**

None. Every member of the affected set is covered or unaffected.

**Tool calls**
1. One Bash call: listed the repo and `dotnet/` root for rule documents (`PATTERN.md`, `CLAUDE.md`, changelog) and grepped `dotnet/src` for callers of `Calculate` and `PipeFlowCalculator`. My context had no search of rule-document files or of callers outside the Reynolds/MeanVelocity grep.

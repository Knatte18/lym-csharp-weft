```yaml
lens: code-pattern-fit
outcome: reported
```

**Lens: code-pattern-fit. Rules checked, held or broken:**

- **Constraints document (`PATTERN.md`, `pattern/`, `CLAUDE.md`, `.editorconfig`):** none of these exist in the repository (the `ls` and `find` over the repo root and `dotnet/` found none). Held: no rule outranks the surrounding code. "Declared PATTERN changes landed" and "A pattern the parent changed" don't apply.
- **Commit messages and departures:** held. The range has one commit, `0891f86`. It carries no `Pattern-Departure:` trailer, and none is needed.
- **Doc comment convention:** held. The existing code uses `/// <summary>` on types and a few members, no file headers, and many public members without docs (`Pipe.From`, `UnitRegistry.TryFind`, `PipeFlowCalculator.Calculate`). The new files follow that: summaries on types and the main members, and no file headers. Line breaks and wrapping are not a finding.
- **Project layout:**
  - held: `src/LymCsharp.Regression` and `tests/LymCsharp.Regression.Tests` mirror the existing `src/` and `tests/` pairs.
  - held: the solution entries, `Directory.Build.props` (`net10.0`, nullable, implicit usings) and NUnit 4 with `[SetUp]` and `[TearDown]` cleanup.
  - held: primary constructors, file-scoped namespaces and braced blocks match the existing code.
- **`.gitignore` and README:** held. `.baseline/` is added, and the README names `dotnet run --project src/LymCsharp.Regression -- <capture|compare> <baseline_dir> [--skip-file <path>]` for both modes, as the decision record specifies.
- **Test field naming:** broken, minor.

**Finding**
- **LOW, `consistency`:** `tests/LymCsharp.Regression.Tests/BaselineHarnessTests.cs:7-8` and `RegressionCommandTests.cs:7-11` name private fields without an underscore (`workDirectory`, `baselineDirectory`, `skipFile`, `valueOfFirst`, `cases`). The existing test projects use `_model`: `tests/LymCsharp.Hydraulics.Tests/FrictionTests.cs:7` and `HaalandFrictionTests.cs:7`. The same file-level convention in the library, `private readonly OutputScrubber scrubber` at `BaselineHarness.cs:19`, also has no underscore. The existing `src/` projects have no private fields to compare against, so this is a test-project convention. The fix is to rename the test fields to `_workDirectory` and so on.

**Tool calls**
1. `ls -a`, `find` and `git log` on the repo root: to see whether `PATTERN.md`, `CLAUDE.md`, `pattern/` or `.editorconfig` exist, and whether the commit has a `Pattern-Departure:` trailer. My context held neither.
2. `head` of `PipeFlow.cs`, `UnitRegistry.cs` and the Hydraulics test files, plus `git ls-files`: to read the existing comment and naming conventions that the new files are measured against. The manifest only had the new files.

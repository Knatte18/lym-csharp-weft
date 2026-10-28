```yaml
lens: code-affected-set
outcome: reported
```

**Lens: code-affected-set. Per change, the set I found:**

- **Solution entries** (`dotnet/LymCsharp.sln`): covered. Both new projects have Project entries, Debug/Release configs for Any CPU, x64 and x86, and NestedProjects mappings to the `src` and `tests` folders. The two GUIDs are unique and match across all three sections.
- **`.gitignore`**: covered. `.baseline/` is added. The README's `.baseline/` advice relies on this line.
- **README command and library list**: covered. The command string matches the decision record's `dotnet run --project src/LymCsharp.Regression -- <capture|compare> <baseline_dir> [--skip-file <path>]`. It is shown as two lines, one per mode, and says "From `dotnet/`", which is where the `--project` path resolves. The library list names `LymCsharp.Regression`. The exit codes it documents (0, 1, 2) match `RegressionCommand`.
- **`RegressionCommand.Run` usage text and exit codes** are asserted in `RegressionCommandTests` and documented in the README. Covered.
- **Baseline file shape** (`<case>.txt`): read by `BaselineHarness.Compare` and asserted in `BaselineHarnessTests`. The README names the same shape. Covered.
- **New `Exe` project depending on the Units, Hydraulics and Reporting libraries**: the project references are in the csproj. `Directory.Build.props` supplies `net10.0`, nullable and implicit usings, and the test csproj uses NUnit 4.3.2 with the `NUnit.Framework` global using. Covered.
- **Existing hydraulics, units and reporting code and tests**: not touched. The diff only adds files and edits the sln, `.gitignore` and README, so no existing caller or assertion changes. Covered.
- **Repository rules and stencils**: there is no `CLAUDE.md`, `PATTERN.md`, stencil or hub drift config in the tracked files, so no repository-rule or stencil finding applies. The hub drift config is out of scope per the record.

**Findings:** none.

**Tool calls (1):** `git diff` of the sln, an `ls`, and a `git ls-files` filter for rule, config and drift files. My context did not hold the sln diff or the repository's tracked rule files.

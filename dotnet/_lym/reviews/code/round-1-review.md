---
verdict: APPROVED
findings:
  - id: F1
    severity: LOW
    class: design
    origin: lens:code-correctness
    location: dotnet/src/LymCsharp.Regression/RegressionCommand.cs:20
    summary: An empty baseline_dir argument passes validation and crashes with an unhandled ArgumentException instead of exiting 2
    fix: In RegressionCommand.Run, add string.IsNullOrWhiteSpace(args[1]) to the invalid-arguments condition so an empty baseline directory writes the usage line and returns UsageError. Add a MalformedArgumentsAreAUsageError row that passes an empty string as the baseline directory; the existing row splits on spaces, so build that args array directly.
  - id: F2
    severity: LOW
    class: design
    origin: lens:code-tests
    location: dotnet/tests/LymCsharp.Regression.Tests/BaselineHarnessTests.cs:60
    summary: The non-invariant-culture test checks two lines of one case, while the acceptance criterion covers every case
    fix: In CasesRenderInInvariantCultureWhateverTheCurrentCulture, capture RegressionCases.All under nb-NO into one directory and under the invariant culture into another, then assert every case file is equal, report line included. Replace the two Does.Contain assertions with that comparison.
  - id: F3
    severity: LOW
    class: design
    origin: lens:code-tests
    location: dotnet/tests/LymCsharp.Regression.Tests/OutputScrubberTests.cs:7
    summary: No test covers OutputScrubber.ForMachine, so machine-path replacement through the harness is unpinned
    fix: In BaselineHarnessTests, add a case whose line contains the baseline directory path, capture it, and assert the file holds <path> in place of the directory. Do not add a per-helper ForMachine test.
  - id: F4
    severity: LOW
    class: consistency
    origin: lens:code-pattern-fit
    location: dotnet/tests/LymCsharp.Regression.Tests/BaselineHarnessTests.cs:7
    summary: Test private fields lack the underscore prefix the existing test projects use
    fix: Rename the private fields in BaselineHarnessTests.cs and RegressionCommandTests.cs to _workDirectory, _baselineDirectory, _skipFile, _valueOfFirst and _cases, matching the _model fields in tests/LymCsharp.Hydraulics.Tests/FrictionTests.cs.
  - id: F5
    severity: NIT
    class: design
    origin: lens:code-correctness
    location: dotnet/src/LymCsharp.Regression/OutputScrubber.cs:46
    summary: Machine-root replacement is a plain substring replace, so /tmp also rewrites /tmpfile
    fix: In OutputScrubber.Scrub, replace a root only where the next character is a path separator or the end of the line, for example with a regex built from Regex.Escape(root) and the lookahead (?=[/\\]|$). Add a row to ScrubsTimestampsMachineRootsAndNumberFormatting for /machine/other versus a root sharing a prefix.
---

Range reviewed: `git diff 687cab1e8f748c64ed28cb3412faea62d56c7b97..HEAD`, one commit.
The diff adds the two projects, the solution entries, the `.gitignore` line and the README command, and touches no existing library behaviour.
I ran `capture`, `compare`, a mutated baseline and a bad skip-file argument through `dotnet run`: exit codes 0, 0, 1 and 2, and the drift line named the case, line number and both values.

### [LOW] Empty baseline directory crashes instead of exiting 2

**Location:** dotnet/src/LymCsharp.Regression/RegressionCommand.cs:20
**Issue:** `compare ""` passes the argument check, then `Path.GetFullPath("")` throws in `OutputScrubber.ForMachine`. The process aborts with exit code 134 and a stack trace; I reproduced it. The record says a bad baseline argument exits 2, and an unexpanded `{baseline_dir}` in a drift command is the likely way to hit this.

### [LOW] Culture test covers one case, not every case

**Location:** dotnet/tests/LymCsharp.Regression.Tests/BaselineHarnessTests.cs:60
**Issue:** The acceptance criterion says every case renders the same under a non-invariant culture. The test asserts two lines of one case file, so the other cases and the culture-sensitive `report =` line are unpinned.

### [LOW] Machine-path replacement through the harness is unpinned

**Location:** dotnet/tests/LymCsharp.Regression.Tests/OutputScrubberTests.cs:7
**Issue:** The scrubber tests use hand-built roots. If `ForMachine` dropped the baseline directory or the temp path, every test would still pass, and the acceptance criterion "replaces machine paths" would be unmet.

### [LOW] Test field names depart from the existing test projects

**Location:** dotnet/tests/LymCsharp.Regression.Tests/BaselineHarnessTests.cs:7
**Issue:** The new fixtures name private fields `workDirectory`, `baselineDirectory` and so on. The existing test projects prefix private fields with an underscore, as in `_model` in `tests/LymCsharp.Hydraulics.Tests/FrictionTests.cs:7`.

### [NIT] Root replacement has no path-boundary check

**Location:** dotnet/src/LymCsharp.Regression/OutputScrubber.cs:46
**Issue:** An ordinal substring replace turns `/tmpfile` into `<path>file` for a root of `/tmp`. No current case renders a path, so nothing is affected today.

## Rejected

- numerics 1 (report line rounded by `ResultFormatter` before `G10`): the record's open risk already accepts a value on a rounding boundary, the margins are at least 0.2, and the report format belongs to the untouched reporting library.
- numerics 2 and code-tests 2 (no rows for 11-digit integers, `-0`, `1e999`, sentence-final numbers): no case renders these shapes, the rewrite is stable between capture and compare, and the acceptance criteria name none of them.
- code-tests 4 (`<absent>` branch of `CaseDeviation.ToString` unasserted): the deviation records are pinned by the harness test and the format by the command test; an extra assertion on a display string adds no coverage the criteria ask for.
- numerics 3: merged into F5.

The focus directive's six points were each reached: scrubber edge cases (no defect on rendered lines), command validation and skip handling (F1 only), compare semantics (correct), culture handling (code correct, test coverage thin: F2), scope (clean), test economy (no duplicates; gaps F2 and F3).

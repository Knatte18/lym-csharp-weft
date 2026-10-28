# Fixer-report, round 1

All five findings are fixed. The test project passes (30 tests).

## Changes

- **F1** (`b6baefc`): `RegressionCommand.Run` rejects a blank baseline directory as a usage error.
  Fixed differently: the review proposed a row in `MalformedArgumentsAreAUsageError`, but that test splits the command line on spaces and cannot build an empty argument.
  A separate `EmptyBaselineDirectoryIsAUsageError` test covers `capture ""` and `compare ""`.
- **F2** (`02cff1a`, same commit as F3): `CasesRenderInInvariantCultureWhateverTheCurrentCulture` captures every case under `nb-NO` and under the invariant culture, and asserts each case file is equal.
  The `nb-NO` assertion is kept, checked before the switch to the invariant culture.
- **F3** (`02cff1a`): `CaptureReplacesTheBaselineDirectoryPathInACaseLine` pins baseline-directory replacement through the harness.
  F2 and F3 share a commit because both edit `BaselineHarnessTests.cs` and I did not split the hunks.
- **F4** (`7142c2f`): private fields in `BaselineHarnessTests.cs` and `RegressionCommandTests.cs` carry the underscore prefix.
- **F5** (`c7ed0cc`, `5e6f94f`): `OutputScrubber` replaces a machine root only at a path boundary.
  Fixed differently: the review proposed the lookahead `(?=[/\\]|$)`.
  I used `(?![\w-])` instead, because the proposed lookahead stops scrubbing a root followed by punctuation or a space (`/tmp: denied`), which the old replace scrubbed.
  Rows added: `/machinery/other` (unchanged), `read /machine/work` (end of line) and `failed in /machine: denied`.

## Self-check

- fix-code-correctness, LOW: roots followed by punctuation are no longer scrubbed — fixed with my own change (F5 above).
- fix-code-correctness, LOW: the `$` branch of the lookahead has no row — fixed (end-of-line row).
- fix-code-correctness, LOW: the culture test lost its `nb-NO` assertion — fixed (assertion restored).
- fix-proposal, LOW: the proposed boundary rule stops scrubbing a root before punctuation or a space — same finding as the first, fixed with my own change.
- fix-affected-set, LOW: private fields `machineRootPatterns` and `scrubber` in `src/LymCsharp.Regression` lack the underscore — no change.
  The review named test files only, and the library projects have no private field with a prefix to match.
- fix-affected-set, LOW: a root followed by `.` or `:` is no longer scrubbed — same finding as the first, fixed.
- fix-consistency: no findings.

## Disputed

Nothing.

## Deferred

Nothing.

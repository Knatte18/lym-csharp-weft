# Add a capture/compare regression harness

Adds `LymCsharp.Regression`, a console project that runs a fixed set of `LymCsharp.Hydraulics` and `LymCsharp.Reporting` cases and checks their output against a captured baseline.
It follows the pattern of `NORCE.Models.NumericalBaseline`, so LoomYard's Drift rows can be proved on this repository before they run on Models.
No existing hydraulics, units or reporting behaviour changes.

## Command

From `dotnet/`:

```
dotnet run --project src/LymCsharp.Regression -- capture <baseline_dir> [--skip-file <path>]
dotnet run --project src/LymCsharp.Regression -- compare <baseline_dir> [--skip-file <path>]
```

- `capture` writes one scrubbed `<case>.txt` per case into `<baseline_dir>`, creating the directory when absent.
- `compare` runs the same cases and prints one `drift:` line per drifted case, naming the case, the first deviating line number and both lines.
  It exits 1 when any case drifted and 0 when none did.
- Exit code 2 is a usage error: an unknown mode, a missing baseline directory, or a skip-file name that matches no case.
  It is distinct from 1, so a misconfigured command never reads as drift.
- `.baseline/` is gitignored for local baselines, and the README documents the command.

## Design decisions

- **Exact comparison of scrubbed text.**
  Scrubbing already fixes number precision, so a tolerance would be a second, competing stability rule.
- **Scrubbing rules.**
  `OutputScrubber` replaces ISO-8601 timestamps with `<timestamp>`, the baseline, current, temp and home directories with `<path>`, and rewrites every free-standing decimal number to 10 significant digits in invariant culture.
  Ten digits absorbs last-bit differences in `Math.Log10` and `Math.Pow` across platforms while still catching a real numerical change.
  Numbers glued to a letter or a dot, such as the 3 in `m3/s` or a version `1.2.3`, are left alone.
  A machine root matches only at a path boundary, and a filesystem root is never replaced.
- **Culture.**
  Cases render under the invariant culture, so `ResultFormatter`'s culture-sensitive formatting is the same on every machine.
- **Compare reports every case.**
  Drift-Verify judges drifted tests one by one, so compare checks all cases in one run and names only the first deviation within each.
  A missing baseline file or a differing line count is a deviation.
- **Skip file.**
  A case is the unit of a skipped test, so `--skip-file` names one case per line; skipped cases are neither captured nor compared, and a missing skip file means no skips.
- **Executable, not a `dotnet test` run.**
  The baseline directory is a positional argument, which avoids run-settings quoting through YAML and a shell, and an exit code is the compare's whole contract.
  The logic is plain library code covered by NUnit.

## Tests

`LymCsharp.Regression.Tests` (NUnit 4) covers capture, the scrubber, compare and the command's exit codes.
It also checks that every case renders the same text twice in a row and under a non-invariant current culture, and that the tests leave no files behind.

## Out of scope

Turning on `drift.enabled` and writing the hub's `capture` and `compare` drift config are left to a later run.

## To check by hand

- A value that sits on a 10-significant-digit rounding boundary could still flip between machines; no current case is known to.
  Capture on one machine and compare on another to confirm.
- Run the command end to end against a real baseline directory; the NUnit tests call `RegressionCommand.Run` in process, not `dotnet run`.

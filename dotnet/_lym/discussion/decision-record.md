# Decision record — regression-harness

## Goal

A capture/compare regression harness in the .NET solution, on the pattern of `NORCE.Models.NumericalBaseline`, that the hub's drift config can name, so LoomYard's Drift rows can be proved on this repository before Models.

## Scope

- A new project `src/LymCsharp.Regression`: a fixed set of regression cases run through `LymCsharp.Hydraulics` and `LymCsharp.Reporting`, an output scrubber, capture and compare, and a command-line entry point.
- A new NUnit project `tests/LymCsharp.Regression.Tests` covering capture, scrub, compare and the command's exit codes.
- Both projects added to `LymCsharp.sln`; `.baseline/` added to the repository's `.gitignore`; the README names the command.
- Out of scope: turning on `drift.enabled` or writing the hub's `capture`/`compare` drift config, and any change to hydraulics, units or reporting behaviour.

## Decisions

### One console command with two modes

Decision: the harness is an executable project run as `dotnet run --project src/LymCsharp.Regression -- <capture|compare> <baseline_dir> [--skip-file <path>]`; its logic is plain library code tested by NUnit.
Rationale: drift's `capture` and `compare` commands must hold `{baseline_dir}` in the command string; a positional argument carries it without `dotnet test` run-settings quoting through YAML and a shell, and an exit code is the compare's whole contract.
"NUnit-hosted" in the board entry is read as NUnit-tested code in the solution.

### One baseline file per case, scrubbed text compared exactly

Decision: each case renders text lines; capture scrubs them and writes `<case>.txt` into the baseline directory; compare renders and scrubs the same case and compares line by line for exact equality.
Rationale: scrubbing already fixes number precision, so a tolerance would be a second, competing stability rule.

### Scrubbing rules

Decision: the scrubber replaces ISO-8601 timestamps with `<timestamp>`, replaces machine-specific roots (current directory, baseline directory, temp directory, home directory) with `<path>`, and rewrites every free-standing decimal number to 10 significant digits in invariant culture.
Cases run under the invariant culture, so `ResultFormatter`'s culture-sensitive formatting is the same on every machine.
Rationale: 10 significant digits absorbs last-bit differences in `Math.Log10` and `Math.Pow` between platforms while still catching any real numerical change.

### Compare reports the first deviation of every case

Decision: compare checks every case, reports for each drifted case its name, the first deviating line number and both lines, and exits 1 when any case drifted, 0 when none did.
A missing baseline file or a differing line count is a deviation.
Rationale: Drift-Verify judges drifted tests one by one, so it needs the full list in one run; within a case only the first deviation is named, as the board asks.

### Skip file names cases

Decision: `--skip-file` names one case per line; a skipped case is neither captured nor compared; a missing skip file means no skips; a name that matches no case fails the command with exit 2.
Bound: a skip removes only the named cases from capture and compare, and an unknown name never silently passes.
Rationale: drift's `{skip_file}` names one skipped test per line, and here a case is the test.

## Constraints

- Code lives under `dotnet/` and targets the solution's `net10.0` via `Directory.Build.props`.
- Tests use NUnit 4 like the existing test projects, and leave no files behind.
- No change to existing library behaviour.

## Auto-mode assumptions

- The hub's drift config is written by a later run that turns drift on; this run only makes the command exist and documents it.
- Exit code 2 for a usage error is distinct from drift's exit 1, so a misconfigured command never reads as drift.

## Open risks

- A value that sits on a rounding boundary at 10 significant digits could still flip between machines; no current case is known to.

## Acceptance criteria

- Capture writes one scrubbed file per case into the given directory, creating it when absent.
- Compare against a fresh capture exits 0 and reports nothing.
- Compare against a baseline with one changed line exits 1 and names the case, the line number and both lines; a case with a missing baseline file is reported as drifted.
- The scrubber replaces timestamps and machine paths and canonicalises numbers, leaving numbers inside words such as `m3/s` alone.
- A skipped case is neither written nor compared; an unknown skip name exits 2.
- An unknown mode or missing baseline directory argument exits 2.
- Every case renders the same text twice in a row and under a non-invariant current culture.

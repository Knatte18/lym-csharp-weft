# Add LymCsharp.Regression, a capture/compare regression harness

Adds a regression harness on the pattern of `NORCE.Models.NumericalBaseline`.
It records a scrubbed baseline of numerical output and checks a later build against it, so LoomYard's Drift rows can be proved on this repository before Models.

## What it does

`src/LymCsharp.Regression` is a console project with one command and two modes, run from `dotnet/`:

```
dotnet run --project src/LymCsharp.Regression -- capture <baseline_dir> [--skip-file <path>]
dotnet run --project src/LymCsharp.Regression -- compare <baseline_dir> [--skip-file <path>]
```

- `RegressionCases.All` is a fixed set of pipe flow cases: water and oil, laminar, transitional and turbulent, both friction models, and every pressure unit in the report.
  Each case renders its inputs, the raw `PipeFlowResult` values and the `ResultFormatter` report line.
- `OutputScrubber` replaces ISO-8601 timestamps with `<timestamp>` and machine roots (baseline, current, temp and home directories) with `<path>`.
  It also rewrites every free-standing number to 10 significant digits in invariant culture.
  Numbers attached to a word, such as the 3 in `m3/s`, are left alone.
- `BaselineHarness.Capture` writes each case's scrubbed lines to `<baseline_dir>/<case>.txt`, creating the directory.
  `BaselineHarness.Compare` re-renders every case and returns, per drifted case, the first deviating line number with the baseline and current lines.
  A missing baseline file or a length mismatch counts as a deviation.
- `RegressionCommand` prints one `drift: <case> line <n>: baseline "...", current "..."` line per drifted case.
  It exits 0 with no drift, 1 on drift and 2 on a usage error, so a misconfigured command never reads as drift.
  A skip file names one case per line to leave out of both modes; a missing skip file skips nothing, and an unknown name exits 2.

These signatures match drift's `capture` and `compare` config, which take `{baseline_dir}` and optionally `{skip_file}`.

## Design decisions

- **Console command, NUnit-tested library.**
  Drift's command string must carry `{baseline_dir}`.
  A positional argument carries it plainly, where `dotnet test` would need run-settings quoting through YAML and a shell, and the gate tiers would need to exclude the cases.
  The board's "NUnit-hosted" is read as NUnit-tested code in the solution.
- **Exact text comparison after scrubbing.**
  Precision is fixed once, by the scrubber, rather than by a separate numeric tolerance.
- **Invariant culture during rendering.**
  `ResultFormatter` and `Quantity.ToString` format with the current culture, so a comma-decimal machine would otherwise write a different baseline.
  The harness sets the invariant culture while a case renders and restores the caller's culture afterwards.
- **Every case is compared, with only its first deviation named.**
  Drift-Verify judges drifted tests one by one, so it needs the full list from one run.
- The hub's drift `capture`/`compare` config is not written here; turning drift on is a later run's job, per the board's out-of-scope list.

## Tests

`tests/LymCsharp.Regression.Tests` covers:

- the scrubber's rules, as one table;
- capture writing one scrubbed file per case;
- a fresh capture of every real case comparing clean;
- rendering under `nb-NO`, which still writes invariant output;
- deviations for a changed, longer, shorter and missing baseline;
- the command's exit codes, output, skip-file handling and usage errors.

## Other changes

- `.baseline/` is gitignored as the local baseline directory.
- The README lists the new project and documents the command.
- No existing library behaviour changes.

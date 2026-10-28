```yaml
lens: code-correctness
outcome: reported
```

Task: lens code-correctness. For each branch the diff adds or changes, I ran a concrete input by hand and give the result below.

**Per-branch list**

- `RegressionCommand.Run` argument validation:
  - `[]`, 1 arg and 3 args: usage error, exit 2. `args[0]` is never read, because `&&` short-circuits on the length test. Correct.
  - `capture dir --skip-file`: length 3, usage error. Correct.
  - `compare dir extra` and `compare dir --skip x`: exit 2. Correct.
  - `restore dir`: exit 2. `is "capture" or "compare"` is a single pattern, so precedence is fine. Correct.
  - `capture ""` (empty baseline dir, for example an unexpanded `{baseline_dir}`): passes validation. `OutputScrubber.ForMachine` then calls `Path.GetFullPath("")`, which throws `ArgumentException` in the `BaselineHarness` field initializer. The process crashes instead of exiting 2. **Not correct (finding F1).**
- Skip-file handling:
  - Missing file: no skips. Correct.
  - Blank lines and CRLF are dropped by `ReadAllLines` plus `Trim`. Correct.
  - Duplicate names collapse in the `HashSet`. Correct.
  - Unknown name: exit 2, and the baseline directory is not created. Correct.
  - A skip file that exists but is unreadable throws an unhandled exception. This is an operational failure but still a non-zero exit. Acceptable.
  - A skip file naming every case: the harness gets zero cases, so capture creates the empty directory and exits 0. Correct.
- `BaselineHarness.Capture`:
  - The directory is created when absent, including nested ones. One `<case>.txt` is written per case. Correct.
  - Files left from an earlier capture for a now-skipped case stay on disk. They are never compared, because compare filters by the same skip list. Correct.
  - The baseline path being an existing file makes `CreateDirectory` throw. This is an unhandled crash with a non-zero exit, not silent. Acceptable.
- `BaselineHarness.Compare` and `FirstDeviation`:
  - Empty baseline vs. one current line: deviation at line 1, baseline null. Correct.
  - Baseline longer or shorter: deviation at `min(len)+1` with the absent side null. Correct.
  - Missing baseline file: treated as `[]`, so it deviates at line 1 unless the case renders no lines. A case rendering zero lines with a missing file reports no drift. No such case exists. Acceptable.
  - Missing baseline directory: every case is reported drifted, exit 1. Consistent with the record.
  - Exit code is 1 only when `deviations.Count > 0`. Correct.
- `RenderScrubbed` culture swap:
  - `CurrentCulture` is swapped, and `.ToList()` forces evaluation inside the `try`. The `finally` restores it. Correct.
  - Lazy enumeration cannot escape the swap. Correct.
  - Cases build their `Pipe`s from literals in static initializers, which are culture-independent. Correct.
  - The `drift:` line is formatted outside the swap, but only a positive int goes through the current culture there. Correct.
- `OutputScrubber` constructor:
  - Filesystem roots (`/`, `C:\`) are dropped.
  - Trailing separators are trimmed, empty entries are removed, and the rest are deduplicated and sorted longest first. Correct.
  - An empty home directory is filtered by the `Length > 0` test. Correct.
- `Scrub` ordering, timestamps then roots then numbers: roots become `<path>`, which has no digits, so the number pass cannot touch it. Correct for the lines the cases render, since none contain paths.
- `Scrub` number rewrite:
  - `4.5E-05` (roughness) is matched whole by the regex and re-emitted as `4.5E-05`. Stable and correct.
  - `-0` stays `-0`, because .NET Core G10 keeps the sign. `-0.0` becomes `-0`. Correct.
  - `m3/s` and `1.2.3` are skipped by the lookarounds. Correct.
  - Integers of 11 or more digits come out as `1.23456789E+10`. This is deterministic on both sides, so it is not a defect. No case emits one.
  - `1e400` parses to infinity on .NET Core 3.0 and later and does not throw. It would print `Infinity`. No case emits one.
  - Leading zeros (`007` becomes `7`) are deterministic. Acceptable.
- `RegressionCases`: `:R` doubles, `Quantity.ToString` and `ResultFormatter.Format` all run inside `Render`, so they run under the swapped culture. No rendered line contains a path or timestamp. Correct.
- `Program.cs`: it returns the `Run` exit code. Correct.

**Findings**

- **F1, LOW, design.** `dotnet/src/LymCsharp.Regression/BaselineHarness.cs:20`, reached through `RegressionCommand.cs:20-25`. An empty baseline-directory argument passes the argument check, then `Path.GetFullPath("")` throws `ArgumentException`. The process dies with an unhandled-exception code instead of the usage error 2. The record says a missing baseline directory exits 2. An unexpanded or empty `{baseline_dir}` from a drift command string is the likely way to hit this. `Run` should treat an empty or whitespace `args[1]` as a usage error. No test covers it.
- **F2, NIT, design.** `dotnet/src/LymCsharp.Regression/OutputScrubber.cs:44-47`. Machine-root replacement is a plain ordinal substring replace with no path-boundary check. A root of `/tmp` turns `/tmpfoo/x` into `<path>foo/x`, and `/home/knatte` turns `/home/knatteb` into `<path>b`. It has no effect on the current cases, because none render paths. Fix by requiring the match to be followed by a separator or the end of the line.

Tool calls: none. My context already held every file the lens needed.

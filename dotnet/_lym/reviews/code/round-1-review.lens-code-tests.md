```yaml
lens: code-tests
outcome: reported
```

Lens: code-tests. Per behaviour, the test that pins it, or none.

**Behaviour → pinning test**
- Capture writes one scrubbed file per case and creates the absent directory: `BaselineHarnessTests.CaptureWritesOneScrubbedFilePerCase` (the baseline path is `nested/baseline`, which does not exist yet). Pinned.
- Compare against a fresh capture exits 0 and prints nothing: `RegressionCommandTests.CompareExitsZeroAndPrintsNothingWhenNothingDrifted`, plus `BaselineHarnessTests.CompareAgainstFreshCaptureOfEveryCaseFindsNoDeviation` over the real cases. This second test is also the only repeat-render equality check for every case. Pinned.
- Compare names the first deviating line of each drifted case, treats a missing file and a differing line count as deviations, and checks every case: `BaselineHarnessTests.CompareReportsTheFirstDeviationOfEachDriftedCase` (changed, longer, shorter, missing, unchanged). Exit 1 and the message format: `RegressionCommandTests.CompareExitsOneAndNamesTheCaseLineAndBothValuesOnDrift`. Pinned.
- `CaseDeviation.ToString` with both lines present: the command drift test. Pinned.
- `CaseDeviation.ToString` rendering `<absent>`: none.
- Scrubber timestamps (both forms), root replacement, longest root first, filesystem root ignored, 10-digit rounding, `1e-5`, negative decimal, `m3/s` and `1.2.3` left alone: `OutputScrubberTests` rows. The "/" root is pinned indirectly by the `l/s` row. Pinned.
- Scrubber very large integers, which `G10` turns into exponent form (e.g. `1234567890123`), negative zero, and overflowing literals such as `1e999`: none.
- `OutputScrubber.ForMachine`, which supplies the baseline directory, current directory, temp directory and home directory as roots: none. No test captures a line containing the baseline path, so `<path>` replacement through the harness is unpinned.
- Culture: `BaselineHarnessTests.CasesRenderInInvariantCultureWhateverTheCurrentCulture` (`[SetCulture("nb-NO")]`). It asserts two lines of one case file and that the current culture is restored. The acceptance criterion says every case, so the other four cases and the culture-sensitive `report =` line are not checked under nb-NO.
- Skipped case neither captured nor compared, with blank lines in the skip file ignored: `SkippedCaseIsNeitherCapturedNorCompared`. Pinned.
- Missing skip file means no skips: `MissingSkipFileSkipsNothing`. Pinned.
- Unknown skip name exits 2, writes nothing, and names the case: `UnknownSkipNameIsAUsageError`. Pinned.
- Unknown mode, missing baseline directory, misordered flag, extra argument, and no arguments all exit 2 with usage text and empty stdout: `MalformedArgumentsAreAUsageError` rows. Pinned.
- Tests leave no files behind: `TearDown` in both fixtures removes the work directory. Pinned.
- Every case produces output that is stable across processes: not applicable, since `RegressionCases.All` content has no direct test beyond the two lines above.

**Findings**

1. LOW / design. `dotnet/tests/LymCsharp.Regression.Tests/OutputScrubberTests.cs:7`. No test covers `ForMachine` or machine-root replacement through `BaselineHarness`. The acceptance criterion "replaces machine paths" is pinned only for hand-built roots. If `ForMachine` dropped the baseline directory or the temp path, every test would still pass. Evidence: `OutputScrubber.cs` `ForMachine`; `BaselineHarnessTests.cs` capture test uses no path line.
2. LOW / design. `OutputScrubberTests.cs:9-18`. The number-canonicalisation rows omit the cases the focus directive names. A large integer (an id or count) that `G10` renders in exponent form, `-0`, and an overflowing literal such as `1e999` (parses to infinity) have no row. Evidence: the `NumberPattern` and `G10` code in `OutputScrubber.cs`, and the row list above.
3. LOW / design. `BaselineHarnessTests.cs:60-72`. The culture test checks two lines of one case, though the criterion says every case under a non-invariant culture. Comparing the whole nb-NO capture with an invariant capture would cover all cases and the `report =` line.
4. NIT / design. `BaselineHarnessTests.cs:74-98` and `RegressionCommandTests.cs`. The `<absent>` branch of `CaseDeviation.ToString` (used for a missing file or a line-count difference) is never asserted. The harness test asserts the record values, not the rendered text.

**Test economy**
- No duplicated test found. The command-level compare tests pin the exit code and output text over fake cases. The harness-level tests pin the deviation records, and the real-case comparison appears once.
- `CaptureWritesOneScrubbedFilePerCase` repeats the scrubber's timestamp and number rows. It is not a duplicate, because it proves capture applies the scrubber.

**Tool calls:** none. Everything came from the inherited context.

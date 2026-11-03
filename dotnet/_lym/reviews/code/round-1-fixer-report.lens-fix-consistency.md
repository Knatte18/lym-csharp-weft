```yaml
lens: fix-consistency
outcome: reported
```

No findings.

Task: check the fix-consistency lens (siblings and twins of the `ResultFormatter` change set).

- `ResultFormatter.Format` has one production caller, `src/LymCsharp.Regression/RegressionCases.cs:68`. Its signature and the one-line output shape are unchanged.
- `OutputScrubber` rewrites free-standing numbers through `double.Parse` and `ToString("G10")`. `NumberPattern` matches the new fixed-point output, such as `0.00000000000001235` and `-123500`. The scrubber then reprints tiny values in exponent form, such as `1.235E-14`. That happens on both the capture and compare sides, so baselines stay consistent. It is a scrubber effect and not a defect in this change set.
- The `<summary>` text on `Format` ("rounded to 4 significant digits and printed without trailing zeros") still matches the new behaviour.
- No other file mentions `RoundToSignificantDigits` or the old formatting, so no twin was left behind.
- The new test rows follow the existing row format and the existing test name.

Tool calls:
1. `grep` for `RoundToSignificantDigits`, `ResultFormatter` and "significant digits": my context lacked the other callers and docs.
2. `Read` of `OutputScrubber.cs`: my context lacked how the regression harness post-processes `Format` output.

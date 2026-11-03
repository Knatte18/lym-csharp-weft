---
round: 2
focus:
  - "In src/LymCsharp.Reporting/ResultFormatter.cs, check that every value Format prints is fixed-point text with no exponent notation (E-05 style) and no trailing zeros, for magnitudes from about 1e-14 up to about 1e15, and that negative values keep their sign and 4 significant digits."
  - "In RoundToSignificantDigits of src/LymCsharp.Reporting/ResultFormatter.cs, check the branch for values needing more than 15 decimals (Math.Round accepts at most 15): the result must keep 4 significant digits and must not collapse a nonzero value to 0 or -0."
  - "In tests/LymCsharp.Hydraulics.Tests/ResultFormatterTests.cs, check that FormatsEveryValueToFourSignificantDigits has rows pinning each ResultFormatter branch: small values below 1e-4, values needing more than 15 decimals, negative values, and values at or above 1e4 that round to a negative decimals count. Flag a row that duplicates another row's branch."
---

Round 1 left three open findings, all in `ResultFormatter` and its test table.
The focus entries ask the round-2 reviewer to re-check those areas fresh, without a stated verdict.

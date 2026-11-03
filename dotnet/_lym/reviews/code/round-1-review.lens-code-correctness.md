```yaml
lens: code-correctness
outcome: reported
```

Task: code-correctness lens over `ResultFormatter.Format` and `RoundToSignificantDigits`, running a concrete input through each branch.

**Cases run**

1. `value == 0` branch. Input `0.0` returns `0` and prints `0`. Correct. Input `-0.0` also hits the branch (`-0.0 == 0` is true) and returns `+0`. Correct.
2. `decimals >= 0` branch, normal values. All correct:
   - `1.23456` → floor(log10)=0, decimals=3 → `1.235`.
   - `-1.23456` → `-1.235`.
   - `0.000123456` → exponent -4, decimals=7 → `0.0001235`.
3. `decimals < 0` branch, normal values. Correct:
   - `123456` → exponent 5, decimals=-2, scale=100, Round(1234.56)*100 = `123500`.
   - `-123456` → `-123500`.
4. Boundary values. Correct: `9999.5` rounds to `10000` and `999.95` to `1000`, so the exponent is not recomputed after rounding and the result is still 4 significant digits.
5. Round-half case. Not correct, see F1. Input `1234.5` (exactly representable) gives decimals=0, and `Math.Round(1234.5, 0)` uses ToEven, so it returns `1234`. Input `12345` gives Round(1234.5)=`1234`, so the result is `12340`. A half-away-from-zero reading expects `1235` and `12350`.
6. Clamp `Math.Min(decimals, 15)`. Not correct, see F2. Input `1.234e-12` has decimals=15 and rounds fine. Input `1.234e-17` has decimals=20, clamped to 15, so Round returns `0` and prints `0`. Input `-1.234e-17` returns `-0.0`, which .NET prints as `-0`.
7. NaN. Not correct, see F4. `Math.Log10(NaN)` is NaN and `(int)NaN` is 0 or unspecified, so decimals is 3 and `Math.Round(NaN, 3)` returns NaN. It prints `NaN` without throwing, so this is acceptable.
8. +Infinity. Not correct, see F4. `Math.Log10(inf)` is inf and `(int)inf` is `int.MaxValue` or `int.MinValue`. Either way decimals ends up negative, scale=`Math.Pow(10, ≥2^31-3)`=inf, and `inf/inf`=NaN. The report prints `NaN` instead of `Infinity`. The `PipeFlowCalculator` code was not read to see whether it can yield infinity.
9. Printing the returned double in `Format`. Not correct, see F3.
   - `0.00001235` (decimals=8) prints as `1.235E-05`.
   - `1e15` prints as `1E+15`. Fixed notation applies only for exponents from -5 up to 14.
   - Printing uses the current culture, so a comma-decimal culture prints `1,235`. The old `:0.###` format was equally culture-sensitive, so this is not a regression.
10. `drop` and `drop.Unit.Symbol`. Correct, the same value and unit as the old `Quantity.ToString` apart from the intended digits. The `ConvertTo` dimension check is unchanged.
11. `Regime` interpolation is unchanged.

**Findings**

- F1, LOW, design, `ResultFormatter.cs:34` and `:39`: `Math.Round` defaults to banker's rounding, so exact half values round to even, as in cases 5. Fix: pass `MidpointRounding.AwayFromZero` in both `Math.Round` calls.
- F2, LOW, design, `ResultFormatter.cs:33-35`: values below about 1e-12 lose significant digits to the 15-decimal clamp, and below about 1e-15 they print `0` or `-0`. Case 6 shows this. It is reachable for a tiny pressure drop in `bar`. Fix: round via scaling above 15 decimals, or return the value unrounded when decimals exceeds 15. Also normalise a `-0.0` result.
- F3, LOW, design, `ResultFormatter.cs:21-23`: the raw `double` interpolation prints exponent notation (`1.235E-05`) outside roughly 1e-5 to 1e15, and uses the current culture. This breaks the report's "no trailing zeros, plain" style, and no test row covers it. Fix: format with `ToString("0.###############", CultureInfo.InvariantCulture)` or `"R"` with a fixed-point format. Culture is not a regression.
- F4, LOW, design, `ResultFormatter.cs:27`: ±Infinity prints `NaN`, because the `(int)` cast and `Math.Pow` overflow turn it into `inf/inf`. Fix: return non-finite values unchanged, with `if (!double.IsFinite(value)) return value;` before the log. Raise it only if `PipeFlowCalculator` can produce infinity, since internal inputs are trusted per the repository's rules.

**Tool calls:** none. The manifest already held the diff and `Quantity`.

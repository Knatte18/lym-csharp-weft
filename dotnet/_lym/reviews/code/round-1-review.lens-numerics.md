```yaml
lens: numerics
outcome: reported
```

Task: numerics lens review of `ResultFormatter.RoundToSignificantDigits` and `Format` in `9017f2d..HEAD`.

**List (input, result)**

- `0` and `-0.0`: `value == 0` is an exact test and returns `0`, which prints "0". Correct.
- `1.23456` (decimals 3): `Math.Round(...,3)` gives the double nearest 1.235, so the default print is "1.235". Correct.
- `123456` (decimals -2): the scale branch gives `Round(1234.56)*100` = 123500, an exact integer. Correct.
- `-1.23456` (negative): `Math.Abs` is used only for the exponent, and `Round` is sign-symmetric. Correct.
- `1e300`: scale is `1e297`, quotient about 1235, and the product is finite. The rounding is correct, but the printing is not (see F1).
- `NaN`: `(int)NaN` is platform-dependent. On x86 it is `int.MinValue`, so `decimals` overflows. On ARM it is 0. Either way the result is NaN and the output is "NaN". Acceptable.
- `+Infinity`: `(int)Infinity` is `int.MaxValue` or `int.MinValue` depending on platform. `decimals` goes hugely negative, `scale` is `Pow(10, huge)` = Infinity, and `Infinity/Infinity` is NaN. It prints "NaN" instead of "Infinity" and the sign is lost (F3).
- `1e-13`: `decimals` is 16 and is clamped to 15, so `Round(1e-13, 15)` gives `1E-13`. The clamp then only allows 2 digits, and anything below 5e-16 prints 0 (F2).
- `9999.6`: rounds to 10000, which is the correct 4 significant digits.
- `12345` (exact tie): `Math.Round` defaults to ToEven, giving `1234.5 → 1234`, so 12340 (F4).

**Findings**

- F1. MEDIUM, design. `ResultFormatter.cs:Format`, which interpolates the rounded doubles. Raw `double.ToString()` switches to exponent notation below about 1e-5 and at 1e15 and above. For example, `0.00001235` prints "1.235E-05" and a 5 Pa drop in bar (5e-5) prints "5E-05 bar". The old `0.###` and `Quantity.ToString` never produced exponent text. The report is meant to be human-readable fixed notation. Evidence: `ResultFormatter.cs` (the three interpolated `RoundToSignificantDigits` calls). The test table stops at 0.0001235, one decade above the switch, so it does not catch this.
- F2. LOW, design. `ResultFormatter.cs`, the `Math.Min(decimals, 15)` clamp. Values below about 1e-12 get fewer than 4 significant digits, and values below 5e-16 print "0". This contradicts "every numeric value to 4 significant digits" and silently turns a tiny nonzero value into 0. Evidence: the clamp comment, which only states the `Math.Round` API limit. The tests have no row for the clamp.
- F3. LOW, design. `ResultFormatter.cs`, the `(int)Math.Floor(Math.Log10(...))` cast. A non-finite input goes through an unspecified-range `(int)` cast and prints NaN for ±Infinity. Fix by returning non-finite values unchanged before the cast, so they print as "Infinity" or "NaN" in the report. This is not a boundary guard on external input: the calculator can produce Infinity, for example from a zero viscosity or diameter, and the formatter should not misreport it.
- F4. LOW, design. `ResultFormatter.cs`, `Math.Round(value, ...)` and `Math.Round(value / scale)`. The default is `MidpointRounding.ToEven`, so exact ties round down half the time (12345 gives 12340, 1.0625 gives 1.062). Report rounding conventionally uses `MidpointRounding.AwayFromZero`. No test row pins a tie.
- F5. NIT, consistency. `ResultFormatter.cs`, `Format`. Interpolating a raw double uses the current culture, which gives a decimal comma and localized NaN/Infinity symbols. The old `{:0.###}` was also culture-sensitive, so this is not a regression. The regression baselines (`RegressionCases.cs`, `report = ...`) are only stable if the culture is fixed to invariant. If the harness does not do that, `CultureInfo.InvariantCulture` is needed.

Tool calls: none; my inherited context already held everything this lens needed.

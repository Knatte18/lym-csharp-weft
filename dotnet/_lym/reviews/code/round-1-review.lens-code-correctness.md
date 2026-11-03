```yaml
lens: code-correctness
outcome: reported
```

Task: run the code-correctness lens over `Quantity.Parse` and the tests the diff adds.

**Per-branch list**

| Branch | Case run | Result |
|---|---|---|
| `Parse` happy path | `"1.5 bar"`, `"7.2 l/min"`: separator at index 3 and 3, `double.Parse` gives 1.5 and 7.2, `Find` returns the catalog unit | Correct in an English-like culture. In a comma-decimal culture the value is wrong; see finding 1. |
| `Parse` round trip | `new Quantity(3.25, Psi).ToString()` is `"3.25 psi"`, which parses back to 3.25 | Correct. |
| `Parse` round trip | A value with more than 3 decimals, such as 1.23456: `ToString` writes `0.###`, giving `"1.235 psi"` | Not equal to the original, so the summary's claim is too broad; see finding 3. |
| no-space branch | `"1.5bar"` throws `FormatException`. `""` has `IndexOf` -1 and throws `FormatException`. | Correct. |
| separator at index 0 | `" bar"` gives `text[..0]` = `""`, and `double.Parse("")` throws `FormatException` | Correct, and covered by the doc's "number part is not a number". |
| trailing space | `"1.5 "` gives unit `""`, and `Find("")` throws `KeyNotFoundException("Unknown unit ''")` | Correct. |
| extra text | `"1.5 bar extra"` and `"1.5  bar"` give unit `"bar extra"` and `" bar"`, both `KeyNotFoundException` | Acceptable; the split is at the first space, as the entry says. |
| unit lookup | `"1.5 BAR"` resolves to the catalog `bar` through the ignore-case match in `UnitRegistry.TryFind` | Correct. |
| `Find` failure after a valid value | `"1.5 furlong"` throws `KeyNotFoundException`. `Parse` holds no state. | Correct, nothing is left inconsistent. |
| `null` | `Parse(null)`: `text.IndexOf` throws `NullReferenceException` | Not a documented exception; see finding 2. |
| overflow and special values | `"1e999 bar"` gives Infinity, because modern .NET does not throw. `"NaN bar"` gives a NaN quantity. | Consistent with what `ToString` can write. No wrong result beyond finding 1. |

**Findings**

1. MEDIUM, `design`, `src/LymCsharp.Units/Quantity.cs:61`.
   - `double.Parse(text[..separator])` uses the current culture and the default number styles, which allow a thousands separator. `"1,5 bar"` in en-US silently becomes 15 bar, and `"1.5 bar"` in de-DE silently becomes 15 bar (`.` is the group separator there). A flow or pressure read from a config file or command line is exactly the input this method is for.
   - The wrong value is returned without any `FormatException`. The entry does not license culture dependence or thousands acceptance, and `ToString`'s `0.###` never writes a group separator, so the lenient style gives nothing back for the round trip.
   - Evidence: `Quantity.cs:45` and `:61`; the repository formats and parses with `CultureInfo.InvariantCulture` at `src/LymCsharp.Reporting/ResultFormatter.cs:35` and `src/LymCsharp.Regression/OutputScrubber.cs:51`.

2. LOW, `design`, `src/LymCsharp.Units/Quantity.cs:55`.
   - `Parse(null)` throws `NullReferenceException`, not one of the two documented exceptions.
   - `Nullable` is enabled, so passing null is a caller contract violation. The entry names no guard, so this is low and arguable.
   - Evidence: `Quantity.cs:53-55`; `Directory.Build.props:5`.

3. LOW, `consistency`, `src/LymCsharp.Units/Quantity.cs:49`.
   - The doc says the number is read "as `ToString` writes it", and the change description claims `Parse(q.ToString())` returns `q` "on any machine".
   - This holds only for values whose `0.###` rendering is exact. `1.23456` round-trips to `1.235`, so the claim is too broad.
   - The entry's own test names only "a quantity such as 3.25 psi".
   - Evidence: `Quantity.cs:45` and `:49`; `_lym/landing/summary.md:14-15`.

4. LOW, `design`, `tests/LymCsharp.Units.Tests/QuantityTests.cs:75`.
   - `ParseCases` and the other tests assume a `.` decimal point under whatever culture the test host runs in. Under a comma-decimal culture, `"1.5 bar"` parses to 15 and the test fails, or an environment variable flips it. This follows from finding 1.
   - The existing `ToStringShowsValueAndSymbol` has the same dependence, so it is not new to the diff.

**Tool calls:** none. The manifest already held `Quantity.cs`, `UnitRegistry.cs`, the tests, the summary and the culture grep hits.

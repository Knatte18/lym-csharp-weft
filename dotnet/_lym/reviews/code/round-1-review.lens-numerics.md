```yaml
lens: numerics
outcome: reported
```

Task: numerics lens review of the `Quantity.Parse` diff (`80e63c9`).

**Per-item list**

1. Round trip `Parse(q.ToString())` returns `q`, tested with 3.25 psi (`QuantityTests.cs:86-91`).
   - Correct for 3.25. `ToString` writes `0.###`, which is exact for 3.25 and for 1.5, so the `Is.EqualTo` on the record struct compares `double ==` safely.
   - For a general value the round trip does not hold. `ToString` rounds to 3 decimals, so `Parse(new Quantity(3.7654, bar).ToString())` gives 3.765 and not `q`.
   - The `Parse` doc comment says it reads "the form `ToString` writes". It does not say it can't recover what the 3-decimal rounding dropped. The summary claim "keeps `Parse(q.ToString())` returning `q` on any machine" is true only for values with at most 3 decimals.
2. `ParseReadsValueAndUnit` compares `new Quantity(7.2, LitrePerMinute)` with `Quantity.Parse("7.2 l/min")` by exact equality (`QuantityTests.cs:75-76`).
   - Correct. `double.Parse("7.2")` returns the nearest double to 7.2, the same as the literal `7.2`.
   - The decision-record Goal asks for exactly this, and there is no arithmetic in between.
3. Unit matching: `UnitRegistry.Find` ignores case (`UnitRegistry.cs:9`), so `1.5 BAR` parses to bar.
   - No unit-mixing problem. The returned `IUnit` is the catalog instance, and `Value` stays in that unit with no conversion.
4. Value range: `double.Parse` accepts `NaN`, `Infinity`, `-5` and `1e999`.
   - `1e999` parses to `Infinity` on .NET Core 3.0 and later, so it does not throw.
   - A negative pressure, flow or length, or a non-finite value, would enter a `Quantity` unchecked.
   - The Goal does not ask for range checks. `Quantity`'s constructor and `ConvertTo` have none either, and the repository has no rules document that mandates guards. No guard is requested; this is a LOW note only, because `Parse` is the first entry point that takes untrusted text.
5. Culture and `double.Parse`: `double.Parse(text[..separator])` uses `NumberStyles.Float | AllowThousands` in the current culture (`Quantity.cs:61`).
   - `ToString` writes in the current culture, so a same-culture round trip holds for 3.25 (comma or dot).
   - Thousands separators are accepted. In en-US, `"1,5 bar"` parses as 15 bar and does not throw.
   - In a decimal-comma culture, `"1.5 bar"` parses as 15 bar. Those are silent wrong values, not a `FormatException`.
   - This matters because the stated purpose is config files and command lines, which are culture-neutral by nature.
   - The rest of the repository formats and parses numbers with `CultureInfo.InvariantCulture`: `ResultFormatter.cs:35`, `OutputScrubber.cs:51-52`, and `BaselineHarness.cs:78-79`, which forces invariant.
   - The Goal says nothing on culture, so the current-culture choice is the writer's own, with a reasoned rationale in the summary.
   - The tests run in whatever culture the host has and pin no culture. On a decimal-comma machine, `Parse("1.5 bar")` would give 15 and fail `ParseReadsValueAndUnit`. The suite is culture-dependent, and that is a determinism risk for the tests.
6. Precision: `text[..separator]` is passed whole to `double.Parse`.
   - There is no cast or narrowing, so there is no precision loss beyond the nearest-double rounding already noted.
   - Leading or trailing whitespace is tolerated by `double.Parse`, so `" 1.5 bar"` splits at index 0 and parses an empty value part. That throws `FormatException`, which is correct.
   - `"1.5  bar"` (two spaces) gives the symbol `" bar"`, so `Find` throws `KeyNotFoundException`. That is consistent with the "one space" contract.

**Findings**

- **MEDIUM, design:** `Quantity.cs:61`. `double.Parse` runs in the current culture and also accepts thousands separators.
  - `"1,5 bar"` silently parses as 15 bar in en-US, and `"1.5 bar"` silently parses as 15 bar in a decimal-comma culture. No exception is thrown.
  - The silent wrong value is the dangerous case for config-file input.
  - It also contradicts the repository's invariant-culture practice at `ResultFormatter.cs:35` and `OutputScrubber.cs:51`.
  - The Goal is silent on culture, so this is the writer's own choice. Severity is MEDIUM because it risks silently wrong physical values, not LOW.
  - Evidence: `Quantity.cs:45,61`, `ResultFormatter.cs:35`.
- **LOW, design:** `QuantityTests.cs:73-103`. The parse tests depend on the host culture, with no pinned culture.
  - The literal `"1.5 bar"` fails on a decimal-comma host.
  - Evidence: `QuantityTests.cs:75-76,96`.
- **LOW, design:** `Quantity.cs:47-49`. The doc comment, and the summary's "returning `q` on any machine", overstate the round trip.
  - `ToString` rounds to 3 decimals, so `Parse(q.ToString())` returns `q` only for values with at most 3 decimals.
  - The test uses 3.25, which hides this.
  - Evidence: `Quantity.cs:45`, `_lym/landing/summary.md:14-15`.
- **LOW, design:** `Quantity.cs:61`. `NaN`, `Infinity`, `1e999` (parses to `Infinity`) and negative values are accepted without a guard.
  - The Goal asks for no guard and the repository has no guard rule, so no guard is requested.
  - It is noted as emphasis only.

**Tool calls:** none. The manifest already held every file this lens needed.

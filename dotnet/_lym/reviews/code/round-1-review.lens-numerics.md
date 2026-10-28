```yaml
lens: numerics
outcome: reported
```

Task: numerics lens review of the regression harness diff.

**Per-behaviour list**
- `OutputScrubber.Scrub` number rewrite (`double.Parse` then `G10`, invariant): safe for the rendered lines. The pattern only matches digit strings, so `Parse` cannot throw. `-0` stays `-0`, `4.5E-05` stays `4.5E-05`, and `NaN` and `Infinity` are not matched. A literal like `1e999` would parse to Infinity and print `Infinity`, but `:R` output cannot produce one.
- Machine roots replaced before the number rewrite: held. `<path>` and `<timestamp>` contain no digits, so the rewrite cannot touch them.
- Case values, worked from `Fluid` and the friction classes:
  - DN100 water at 10 l/s: Re ≈ 127069, turbulent.
  - Oil at 0.1 l/s: Re ≈ 22, laminar.
  - PVC 2 in water at 7.2 l/min: Re ≈ 3002, transitional.
  - 6 in oil at 36.5 l/s: Re ≈ 5306, turbulent.
  - All values are physical and positive. No case sits near the 2300 or 4000 thresholds.
- Unit conversions in `RegressionCases`: consistent (mm, in and ft all go through `Pipe.From` to metres). Nothing mixed.
- Comparison: `BaselineHarness` compares text, never `double ==`. Held.

**Findings**

1. NIT, design, `dotnet/src/LymCsharp.Regression/RegressionCases.cs:58`, `OutputScrubber.cs:51`: the report line gets its own rounding before the scrubber runs.
   - `ResultFormatter.Format` rounds with `0.###` (velocity) and `0` (Re) first. `G10` then cannot absorb a last-bit difference there, because it only sees the already-rounded text. A value near a half-way point would flip the printed text across machines.
   - The decision record's rationale ("10 digits absorbs last-bit differences") covers the raw lines only.
   - The current cases have margins of 0.2 or more from a boundary (Re ≈ 127069.3, 3001.9, 5306.2; velocity ≈ 1.2732, 0.0592, 2.0010), so nothing flips today.
   - Evidence: `ResultFormatter.cs`, the `Format` method.

2. NIT, design, `dotnet/src/LymCsharp.Regression/OutputScrubber.cs:51`: `G10` has two edge behaviours that no test pins.
   - Integer tokens of 10 or more digits (ids, counts) are rewritten to exponent form with digits dropped, e.g. `12345678901` becomes `1.23456789E+10`. The rewrite is stable between capture and compare, so it causes no false drift. It does make a change in the 11th digit invisible.
   - A number at the end of a sentence (`value 5.`) is rejected by `(?![\w.])` and stays unscrubbed. The current cases produce neither shape.
   - The test file has no row for either.

3. NIT, design, `dotnet/src/LymCsharp.Regression/OutputScrubber.cs:20-25`: machine-root replacement is a plain substring `Replace`.
   - A root of `/tmp` also rewrites `/tmpfile` to `<path>file`, and `/home/user` rewrites `/home/username`.
   - No current case line renders paths, so nothing is affected today.

**Tool calls**
1. Read `PipeFlow.cs`, `Fluid.cs`, `SwameeJainFriction.cs` and `HaalandFriction.cs`: my context lacked the fluid constants and the Reynolds and regime formulas needed to check the case values.

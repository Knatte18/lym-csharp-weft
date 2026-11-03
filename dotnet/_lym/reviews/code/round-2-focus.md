---
round: 2
focus:
  - "src/LymCsharp.Units/Quantity.cs, method Parse: settle whether the value part is read with number styles that reject a thousands separator, so that `1,5 bar` in en-US and `1.5 bar` in de-DE throw FormatException instead of returning 15, while the current-culture reading stays."
  - "tests/LymCsharp.Units.Tests/QuantityTests.cs: settle whether a test reaches Parse's non-numeric value path (a value part such as `abc` with a valid symbol and a space), so that breaking that path fails a test."
  - "tests/LymCsharp.Units.Tests/QuantityTests.cs: settle whether the current-culture reading is pinned by a test that sets CultureInfo.CurrentCulture and restores it, and whether the other parse tests no longer depend on the host culture."
  - "_lym/landing/summary.md, Design decisions bullet about culture: settle whether the round-trip claim for `Parse(q.ToString())` is limited to values that `ToString` (format `0.###`) writes exactly."
---

Each entry is a place to look and a question to settle, not a verdict.

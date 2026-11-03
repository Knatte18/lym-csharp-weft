```yaml
lens: fix-affected-set
outcome: reported
```

Task: fix-affected-set lens. I reran the set searches for F1 and F3, and I checked F4's claim wording.

**F1 (value-read sites).** `grep` for numeric parses found two sites. `Quantity.cs:63` is fixed. `OutputScrubber.cs:51` already passes `CultureInfo.InvariantCulture`. It parses scrubbed report output, outside the finding's premise (`Quantity.Parse` and `Quantity.ToString`). No other `double.Parse`, `TryParse` or `Convert.ToDouble` site exists in `src` or `tests`. No finding.

**F3 (parse tests pinned to a culture).** Every `Quantity.Parse` call sits in `QuantityTests.cs` (lines 83, 91, 97, 105, 117, 118). The class-level `[SetCulture("")]` covers all of them. The de-DE test overrides it at method level. The pre-existing `ToStringShowsValueAndSymbol` is also pinned now, which the review excluded but does no harm. There are no `Parse` callers in `src` or in other test projects. No finding.

**F4 (round-trip claims).** The only "any machine" and round-trip claims are in `_lym/landing/summary.md`. The fixer narrowed that one and added the thousands-separator bullet. The grep skipped `.lym` and found no other doc with the claim. No finding.

**Unchecked:** F2's set is the `FormatException` rows. It holds one row per failure path (no space, non-numeric, thousands separator), and I found no further path in `Parse`. No finding.

No findings.

Tool calls:
1. `grep` for numeric parse sites in `src` and `tests`: context held only the two files read, not the rest of the tree.
2. `grep` for `Quantity.Parse` callers, "any machine", "round trip" and "thousands" across `.cs` and `.md`: context could not show callers or claims in files the fixer never read.

---
verdict: APPROVED
findings:
  - id: F1
    severity: MEDIUM
    class: design
    origin: lens:code-correctness
    location: src/LymCsharp.Units/Quantity.cs:61
    summary: Parse reads the value with default number styles, so a thousands separator is accepted and a config value such as 1,5 bar silently becomes 15 bar
    fix: In Quantity.Parse replace double.Parse(text[..separator]) with double.Parse(text[..separator], NumberStyles.Float, CultureInfo.CurrentCulture) and add using System.Globalization. This keeps the current-culture reading that the summary and the doc comment state, and drops NumberStyles.AllowThousands, so 1,5 in en-US and 1.5 in de-DE throw FormatException instead of returning 15. The doc comment's FormatException line already covers this.
  - id: F2
    severity: MEDIUM
    class: design
    origin: lens:code-tests
    location: tests/LymCsharp.Units.Tests/QuantityTests.cs:99
    summary: The non-numeric value path that Parse documents and adds has no test, because the only FormatException test fails at the missing space first
    fix: Turn ParsingTextWithoutSpaceThrows into a TestCase table, or add rows to one table, so it also covers a value part that is not a number, for example abc bar, and rename it to say it covers text that cannot be split or read. Keep the 1.5bar row. Assert FormatException for every row.
  - id: F3
    severity: LOW
    class: design
    origin: lens:code-tests
    location: tests/LymCsharp.Units.Tests/QuantityTests.cs:73
    summary: The documented current-culture reading is pinned by no test and the parse tests pass or fail with the host culture
    fix: Add one test in QuantityTests that sets CultureInfo.CurrentCulture to de-DE and restores the original in a finally block, then asserts Parse of the 3.25 psi ToString output equals the quantity and that Parse of the text 1.5 bar throws FormatException once F1 is applied. Name in the test that it changes process-global state, as BaselineHarnessTests.cs:64-71 does. Pin the culture of ParseCases and the other parse tests to InvariantCulture the same way.
  - id: F4
    severity: LOW
    class: consistency
    origin: lens:code-correctness
    location: _lym/landing/summary.md:14
    summary: The change description says Parse of ToString output returns the quantity on any machine, but ToString rounds to 3 decimals so 1.23456 psi comes back as 1.235 psi
    fix: In the summary's Design decisions bullet about culture, limit the claim to values whose 0.### text is exact, for example write that it keeps Parse of ToString output returning the quantity on any machine for values with at most 3 decimals.
---

### [MEDIUM] Value part accepts thousands separators and misreads delimiters

**Location:** `src/LymCsharp.Units/Quantity.cs:61`
**Issue:** `double.Parse(string)` uses `NumberStyles.Float | NumberStyles.AllowThousands`. In en-US `1,5 bar` parses as 15 bar, and in de-DE `1.5 bar` parses as 15 bar, with no exception.
`ToString` writes `0.###`, which never emits a group separator, so the lenient style buys nothing for the round trip.
The entry asks for `FormatException` on malformed text and licenses no silent misreading.
The current-culture choice itself matches `ToString` and the entry's round-trip aim, and is not the defect.

### [MEDIUM] Non-numeric value path has no test

**Location:** `tests/LymCsharp.Units.Tests/QuantityTests.cs:99`
**Issue:** The diff documents and adds a `FormatException` for a value part that is not a number (`Quantity.cs:51,61`).
`ParsingTextWithoutSpaceThrows` uses `1.5bar`, which throws at the missing space before `double.Parse` runs, so breaking the value path fails no test.
Rubric test economy: a behavior the diff adds with no coverage.

### [LOW] Culture handling is untested and the tests depend on the host culture

**Location:** `tests/LymCsharp.Units.Tests/QuantityTests.cs:73`
**Issue:** Changing the value read to `InvariantCulture` breaks no test, so the documented current-culture reading is unpinned.
`ParseCases` and the unknown-symbol test hold a `.` decimal point, so on a decimal-comma host `1.5 bar` parses as 15 and the tests fail.
`ToStringShowsValueAndSymbol` has the same host dependence, which predates this diff and is not part of the fix.

### [LOW] Summary overstates the round trip

**Location:** `_lym/landing/summary.md:14`
**Issue:** The summary says `Parse(q.ToString())` returns `q` on any machine.
`ToString` rounds to 3 decimals, so `new Quantity(1.23456, UnitCatalog.Psi)` comes back as 1.235 psi.
The entry asks for the round trip only for a quantity such as 3.25 psi, and the code matches that.

## Rejected

- Null `text` throws `NullReferenceException`, not a documented exception (lens:code-correctness, LOW): `Nullable` is enabled so null is a caller contract violation, the entry names no guard, and the repository has no rule asking for one.
- NaN, Infinity, `1e999` and negative values accepted without a range guard (lens:numerics, LOW): the entry asks for no guard, and `Quantity`'s constructor and `ConvertTo` have none either.
- The lens code-affected-set returned no finding, and I confirmed it: no caller, doc, fixture, baseline or rule document is left wrong, there is no `PATTERN.md`, and the commit carries no `Pattern-Departure:` trailer.
- Doc comment convention: the `<summary>` plus `<exception>` form matches the existing public types, and each claim in it is borne out by the code except what F1 and F3 cover. No finding.
- Scope: the diff touches only `Quantity.cs` and `QuantityTests.cs`, adds `Parse`, changes no output, and every case the entry lists has a test. No scope finding.

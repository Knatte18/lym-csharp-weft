```yaml
lens: fix-code-correctness
outcome: reported
```

Task: run the changed `Quantity.Parse` and the added or changed tests step by step, and check callers and test strength (lens fix-code-correctness).

No findings.

`Quantity.Parse` runs as follows:
- `"1.5 bar"` in invariant culture: the value part `1.5` reads as 1.5 under `NumberStyles.Float`, and the unit lookup is unchanged.
- `"1,5 bar"` in invariant culture: `Float` has no `AllowThousands`, so `double.Parse` throws `FormatException`, which is the F1 behaviour.
- `"abc bar"`: `double.Parse` throws `FormatException`.
- `" 1.5 bar"`: the separator sits at index 0, the value part is empty, and `double.Parse` throws `FormatException`. Before the fix it threw the same way.
- `"1.5bar"`: it throws at the missing space, as before.
- Culture handling in `de-DE`: `ToString` writes `3,25 psi`, which `Parse` reads back as 3.25. `1.5 bar` throws, because `.` is neither a decimal separator nor an allowed thousands separator.

I did not search for other callers. The review states that `Parse` is new and the summary says nothing calls it yet. The signature and the documented exceptions are unchanged, and `using System.Globalization;` is added. The gate build ran with 0 warnings and 29 tests passed.

Each added or changed test fails with its defect in place:
- **`1,5 bar` row:** with the old default `double.Parse(string)`, it parses as 15 and throws nothing, so the row fails.
- **`abc bar` row:** with the value read removed or made lenient, the row would not throw. It also pins the path that the `1.5bar` row never reached.
- **`de-DE` test:**
  - If the value is read with `InvariantCulture`, `3,25` becomes 325 under the old thousands style, or throws under `Float`, so the round-trip assert fails.
  - If the value is read with the old defaults in `de-DE`, `1.5 bar` parses as 15 and throws nothing, so the throw assert fails.
  - `Assert.Throws` inside `Assert.Multiple` is valid, and a failing assert is still recorded.
- **Class-level `[SetCulture("")]`:** the method-level `de-DE` attribute overrides it. NUnit restores the culture after each test. The existing `nb-NO` test already uses the same attribute.

The `de-DE` test needs ICU on the host. The existing `nb-NO` regression test has the same requirement, and it passed here.

Tool calls: none.

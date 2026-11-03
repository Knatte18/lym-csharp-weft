```yaml
lens: code-affected-set
outcome: reported
```

Task: lens code-affected-set. I checked what the `Quantity.Parse` diff forces outside the lines it touches.

**Per change, the set found:**

- **New public `Quantity.Parse(string)` (`Quantity.cs:53`)**
  - **Callers:** the grep for `Quantity.Parse` hit only `QuantityTests.cs:82,90,96,102`, so no production caller exists. That matches the entry's "no caller uses `Parse` yet". Covered.
  - **Callee `UnitRegistry.Find` (`UnitRegistry.cs:14`):** it throws `KeyNotFoundException` for an unknown symbol and matches case-insensitively. The `Parse` doc comment's `KeyNotFoundException` line and the summary's case-insensitivity claim both agree with it. `UnitRegistryTests.cs:24` already pins `Find` itself. Covered.
  - **Tests asserting the new behaviour:** `QuantityTests.cs:73-103` (all cases the entry lists). Covered.
  - **Docs and fixtures:** `README.md` describes `LymCsharp.Units` only as "unit conversion for pressure, length and flow" and lists no per-method API, so nothing there goes stale. There is no regression-harness case, fixture or baseline touching `Quantity`. The grep shows `Regression` and `Reporting` don't reference `Quantity.Parse` or `ToString`. Covered.
  - **Output changes:** none. `ToString` is unchanged, and `ResultFormatter` and the regression baselines are unaffected, as the entry says. Covered.
- **Repository rule documents:** there is no `CLAUDE.md`, `PATTERN.md` or `pattern/` in the repository. `CLAUDE.local.md` holds LoomYard's workflow instructions, not repository rules for this diff. So "Declared PATTERN changes landed" and "The repository's own rules" give nothing to check. The repository's existing comment convention is `///` `<summary>` on public types, and the new doc comment follows it.
- **Commit message and departures:** commit `80e63c9` has one commit message with no `Pattern-Departure:` trailer. The diff shows no difference from the entry that would need one. Covered.
- **Change description (`_lym/landing/summary.md`):** its claims match the code.
  - It says the text splits at the first space. `IndexOf(' ')` does that.
  - It says a value part that is not a number gives `FormatException`. `double.Parse` does that.
  - It says the value is read in the current culture, and the code does exactly that.
  - Its test list matches `QuantityTests.cs`.

**Findings:** none.

**Tool calls:** none. Everything I needed was already in the manifest reads and the grep results.

```yaml
lens: fix-affected-set
outcome: reported
```

No findings.

- **F1 (floating-point sweep loops):** The search for `exponent +=` and similar float-stepped loops found no other site. The only sweep is the one the fix changed. The integer-indexed loop gives 441 points, the first exactly at Re 4000 and the last at Re 1e8.
- **F2 (throw sites without a test row):** `ColebrookFriction.cs:38` is the only `InvalidOperationException` in `src` and `tests` for Hydraulics, and `NonConvergingInputThrows` now covers it. The remaining throws are `ArgumentOutOfRangeException` sites in the sibling models and `PipeFlow`. The review did not name them and the fix did not touch them, so they are outside this lens.
- **Left unchecked:** none.

Tool calls:
1. `grep` over `src/LymCsharp.Hydraulics` and `tests/LymCsharp.Hydraulics.Tests` for float-stepped loops, `Math.Log10(4000)` and `throw new`. My context did not hold the sibling files, so I could not rule out other sweeps or throw sites without it.

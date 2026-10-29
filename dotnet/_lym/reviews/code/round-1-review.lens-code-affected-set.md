```yaml
lens: code-affected-set
outcome: reported
```

Task: the code-affected-set lens, covering what `ColebrookFriction` (new) forces outside its own lines.

**Per change, the set found and whether it is covered:**

- **New public type `ColebrookFriction : IFrictionModel`, with an `<inheritdoc/>` doc comment on `FrictionFactor`.**
  - `IFrictionModel` has one member, and only the explicit models and the `ColebrookFriction` tests implement or use it. Covered.
  - `PipeFlowCalculator` takes an `IFrictionModel` by constructor, and its default stays `SwameeJainFriction` (`PipeFlow.cs:16`), as the board entry and decision record require. Nothing needs wiring. Covered.
  - `RegressionCases.cs:31-43` builds its cases from `SwameeJainFriction` and `HaalandFriction` only. A new case there would put the Colebrook model in the regression harness, but the board entry's Scope says "Nothing in `LymCsharp.Regression`". Leaving it untouched is correct. Covered.
  - `README.md` lists only the four libraries and names no friction model, so no doc is made wrong by the addition. Covered.
- **New `[Category("Slow")]` test.**
  - `README.md:12` says slow tests carry `[Category("Slow")]`, and the sweep follows that. Covered.
  - I did not read the gate's tier config, which decides how the `Slow` category is included or excluded, so it is out of my set.
- **Repository rule documents.**
  - The search found no `CLAUDE.md`, `PATTERN.md` or `pattern/` directory under `friction-colebrook` to depth 3. No declared PATTERN changes or stencil changes apply. Covered.
- **Commit messages and departures.**
  - The one commit, `f4dae8f`, carries no `Pattern-Departure:` trailer, and none is needed. Covered.
- **Comment convention.**
  - The class summary follows the sibling one-line summaries, and `<inheritdoc/>` on the implementation matches the `csharp-comments` rule. The sibling classes' `FrictionFactor` has no doc comment, which is their own gap and not part of this diff. Covered.

No findings.

**Tool calls (2):**
1. A `find` for `CLAUDE.md`, `PATTERN.md` and `pattern`, plus a `grep` for friction-model references across `.md`, `.cs`, `.csproj` and `.json`, and `README.md` itself. My context lacked the repo-wide consumer set and the rule documents.
2. A `grep` of `RegressionCases.cs` and `PipeFlow.cs` for the friction lines, to see how the sibling models are consumed, which the manifest lacked.

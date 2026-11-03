## Goal

```markdown
`ResultFormatter` (`src/LymCsharp.Reporting/ResultFormatter.cs`) formats the pipe flow report's numbers.

## Expected

- The report prints every numeric value to 4 significant digits.
- The formatter's tests follow the new format.
- The regression cases that render the formatted report now drift on purpose, and the run records that drift as intended.

## Why

This run proves that Drift reports an intended output change and that the run's Drift verify accepts it with a stated reason, on a hub whose anchor is `dotnet/`.
```

## Scope

## Decisions

## Constraints

## Auto-mode assumptions

## Open risks

<!-- lym:carry-over Code-Review -->
- Code-Review, round 1: closed by an accepted circling decision; a finding below was addressed by the round's fixer or is still unfixed.
  Review: _lym/reviews/code/round-1-review.md; fixer report: _lym/reviews/code/round-1-fixer-report.md.
  - formatter-exponent-notation (class design, severity MEDIUM, open in rounds 1)
<!-- /lym:carry-over Code-Review -->

## Acceptance criteria

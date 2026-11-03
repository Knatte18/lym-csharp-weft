# Note transitional flow in the pipe flow report

`transitional-report-note` · Task · enhancement · done

## Goal

Warn the reader of a pipe flow report when the flow is transitional.
Between a Reynolds number of 2300 and 4000 no friction model is reliable, and the report line gives no sign of it.

## Change

- In `ResultFormatter.Format` (`dotnet/src/LymCsharp.Reporting/ResultFormatter.cs`), append ` (transitional: friction factor uncertain)` to the line when `result.Regime` is `FlowRegime.Transitional`.
  For example: `Transitional flow, v = 0.05 m/s, Re = 3000, dP = 0.1 psi (transitional: friction factor uncertain)`.
- Laminar and turbulent lines stay exactly as they are.
- Update the `Format` doc comment to name the note.

## Intended output change

This change moves regression output on purpose, in one place only:
the `report = …` line of each regression case whose flow is transitional gains the note.
Every other line of those cases, and every line of the laminar and turbulent cases, stays as it is.

## Tests

- Add a transitional case to `ResultFormatterTests.FormatsEveryValueToFourSignificantDigits`, or a test beside it, that expects the note.
- The existing laminar and turbulent cases stay unchanged.

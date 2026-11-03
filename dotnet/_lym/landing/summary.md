# Note transitional flow in the pipe flow report

`ResultFormatter.Format` now ends the report line of a transitional flow with ` (transitional: friction factor uncertain)`.
Between a Reynolds number of 2300 and 4000 no friction model is reliable, and the line gave no sign of it.
For example: `Transitional flow, v = 0.05 m/s, Re = 3000, dP = 0.1 psi (transitional: friction factor uncertain)`.

Laminar and turbulent lines are unchanged.
The `Format` doc comment names the note.

## Design decisions

- The note is appended in `Format`, keyed on `result.Regime == FlowRegime.Transitional`, so it follows the regime classification and adds no Reynolds-number threshold of its own.
- The note is part of the formatted line, so every caller of `Format` gets it.

## Tests

`ResultFormatterTests.FormatsEveryValueToFourSignificantDigits` gains a transitional case that expects the note.
The existing laminar and turbulent cases are unchanged.

## Intended output change

Regression output moves in one place: the `report = …` line of each transitional case gains the note.
`water-pvc-2in-transitional` and `water-pvc-2in-transitional-churchill` show this drift, and no other line of any case moved.

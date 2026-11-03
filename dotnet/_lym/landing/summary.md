# Note transitional flow in the pipe flow report

Between a Reynolds number of 2300 and 4000 no friction model is reliable,
but the pipe flow report line gave the reader no sign of it.

`ResultFormatter.Format` now appends ` (transitional: friction factor uncertain)` to the line when `result.Regime` is `FlowRegime.Transitional`, for example:

```
Transitional flow, v = 0.05 m/s, Re = 3000, dP = 0.1 psi (transitional: friction factor uncertain)
```

Laminar and turbulent lines are unchanged.
The `Format` doc comment names the note.

## Intended output change

The `report = …` line of the two transitional regression cases, `water-pvc-2in-transitional` and `water-pvc-2in-transitional-churchill`, gains the note.
Every other line of those cases, and every line of the laminar and turbulent cases, stays as it is:
the change touches formatting only, not the calculation.

## Tests

`ResultFormatterTests.FormatsEveryValueToFourSignificantDigits` gains a transitional row that expects the note.
Its laminar and turbulent rows are unchanged and pin that those lines carry no note.

# Drift verify report

Compare log: `_lym/drift/logs/compare-20261103T193157.155Z.log`, baseline `fd21dda`, compared head `6b1db61`.

## Reasoning

The task appends ` (transitional: friction factor uncertain)` to the `report = …` line when the regime is transitional, and names that as its one intended output change.
The compare shows drift in exactly the two transitional cases the baseline report expected, each on line 12 (`report = …`) only.
In both, the current line is the baseline line with the note appended, character for character as the decision record specifies.
No laminar or turbulent case drifted, and no other line of the transitional cases moved, so the calculation is untouched.

## Acceptances

- `water-pvc-2in-transitional`: the task intends the report line of transitional cases to gain the note; only that line moved, and the appended text matches the decision record.
- `water-pvc-2in-transitional-churchill`: same reason.

## Fixes

None; no unintended drift.

## Budget

One `lym drift compare` run after the acceptances; it passed (`_lym/drift/logs/compare-20261103T193222.046Z.log`).

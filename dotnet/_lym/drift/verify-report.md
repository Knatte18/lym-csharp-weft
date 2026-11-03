# Drift verify report

## Verdict

Pass.
Every drift is intended and accepted, and no fixes were needed.
The final `lym drift compare` passed (`_lym/drift/logs/compare-20261103T163744.142Z.log`).
Harness budget used: 1 of 8 runs.

## Reasoning

The task, commit `4d40f65`, changes only `ResultFormatter.Format` so that velocity, Reynolds number and pressure drop print to 4 significant digits.
The baseline report expected only line 12 (`report = ...`) of each case to drift.

The compare reports the first deviating line of each case.
Every drifted case deviates first at line 12,
so lines 1–11, which hold the raw `PipeFlowCalculator` values and inputs, match the baseline.

Each new value is the baseline value rounded to 4 significant digits, as the task requires (for example `Re = 127069` becomes `127100` and `dP = 0.078 bar` becomes `0.07793 bar`).

`oil-steel-6in-turbulent` did not drift.
Its baseline report reads `v = 2.001 m/s, Re = 5306, dP = 1.299 bar`.
The old formats already happened to give 4 significant digits for these values, so the new format prints the same line.

## Acceptances

| Test | Reason |
| --- | --- |
| `water-steel-dn100-swamee-jain` | Only the report line changed, to 4 significant digits (Re 127069 → 127100, dP 7925.267 Pa → 7925 Pa); raw lines 1–11 match the baseline. |
| `water-steel-dn100-haaland` | Only the report line changed, to 4 significant digits (Re 127069 → 127100, dP 0.078 → 0.07793 bar); raw lines 1–11 match the baseline. |
| `oil-steel-dn100-laminar` | Only the report line changed, to 4 significant digits (v 0.013 → 0.01273, Re 22 → 22.15, dP 0.102 → 0.1019 kPa); raw lines 1–11 match the baseline. |
| `water-pvc-2in-transitional` | Only the report line changed, to 4 significant digits (v 0.059 → 0.05921, dP 0.007 → 0.006776 psi); raw lines 1–11 match the baseline. |
| `water-pvc-2in-transitional-churchill` | Only the report line changed, to 4 significant digits (v 0.059 → 0.05921, dP 0.007 → 0.006546 psi); raw lines 1–11 match the baseline. |

## Fixes

None.

## Note

The baseline report describes the scope as `partial` with every case skipped,
but `_lym/drift/scope.json` records `full` with no skip.
The compare ran every case, so this verify judged every case directly.

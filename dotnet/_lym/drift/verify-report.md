# Drift verify report

```yaml
baseline_commit: c84741ffb09953e682d6bb505be08749960c86b3
scope: full
compare_runs_used: 1
result: passed
```

## Reasoning

The first compare (`compare-20261103T153027.950Z.log`) reported one drifted test, `water-pvc-2in-transitional-churchill`, whose lines are absent from the baseline.
The decision record asks `RegressionCases` to gain one case that runs a pipe flow through `ChurchillFriction`,
and the baseline report names that new case as the one expected difference.
No existing case drifted, so the task changed no existing behavior.

## Acceptances

- `water-pvc-2in-transitional-churchill`: the new regression case the task adds to run a pipe flow through `ChurchillFriction`.
  The baseline at `c84741f` predates it, so every line reads as absent.

## Fixes

None; no drift was left to fix.

## Recheck

`lym drift compare` after the acceptance passed (`compare-20261103T153053.553Z.log`).

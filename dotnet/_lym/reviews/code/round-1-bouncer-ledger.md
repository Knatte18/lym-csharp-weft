---
round: 1
ledger:
  - key: slow-sweep-misses-re-1e8
    rounds: [1]
    status: open
    class: design
    severity: LOW
    fix: mechanical
  - key: cap-exhaustion-throw-untested
    rounds: [1]
    status: open
    class: scope
    severity: LOW
    fix: mechanical
---

Round 1 raised two LOW findings.
`slow-sweep-misses-re-1e8` (F1): the floating-point exponent loop in `ConvergesAcrossReynoldsAndRoughnessGrid` ends near Re 9.8e7 instead of 1e8.
`cap-exhaustion-throw-untested` (F2): no test pins the iteration-cap `InvalidOperationException`.
Neither is gating at MEDIUM or worse, so the loop converged in round 1.

---
round: 1
ledger:
  - key: reynolds-nonpositive-flow-untested
    rounds: [1]
    status: open
    class: design
    severity: LOW
    fix: mechanical
---

Round 1 raised one finding, F1: the public `Reynolds` method guards against a non-positive flow, but no test covers that guard.
The review's fix text names the exact test, so the fix is mechanical.
No prior ledger exists, so nothing carries forward.

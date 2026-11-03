---
round: 1
ledger:
  - key: calculator-cache-use-untested
    rounds: [1]
    status: open
    class: design
    severity: MEDIUM
    fix: mechanical
  - key: calculator-doc-names-only-turbulent
    rounds: [1]
    status: open
    class: consistency
    severity: LOW
    fix: mechanical
---

Round 1 raised two findings.
The first is a missing `PipeFlowTests` row that would fail if `PipeFlowCalculator` stopped routing `Calculate` through the cache; the review's fix text fully specifies the test.
The second is a doc comment in `PipeFlow.cs` that says "turbulent" where the cache covers every Reynolds number of 2300 and above; the fix text gives the wording.
Both are local corrections, so neither needs a fresh design review.

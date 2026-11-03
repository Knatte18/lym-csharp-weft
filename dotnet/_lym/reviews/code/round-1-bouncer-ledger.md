---
round: 1
ledger:
  - key: formatter-exponent-notation
    rounds: [1]
    status: open
    class: design
    severity: MEDIUM
    fix: mechanical
  - key: formatter-clamp-tiny-values
    rounds: [1]
    status: open
    class: design
    severity: LOW
    fix: mechanical
  - key: formatter-negative-value-test
    rounds: [1]
    status: open
    class: design
    severity: LOW
    fix: mechanical
---

Round 1 raised three findings against `ResultFormatter`, all `design`.
F1 (exponent notation below 1e-4) maps to `formatter-exponent-notation` and gates.
F2 (the 15-decimal clamp) maps to `formatter-clamp-tiny-values`.
F3 (no negative-value test row) maps to `formatter-negative-value-test`.
No ledger key recurs and there is no prior ledger.

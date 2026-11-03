---
round: 1
ledger:
  - key: parse-value-accepts-thousands-separators
    rounds: [1]
    status: open
    class: design
    severity: MEDIUM
    fix: mechanical
  - key: parse-non-numeric-value-untested
    rounds: [1]
    status: open
    class: design
    severity: MEDIUM
    fix: mechanical
  - key: parse-culture-untested-host-dependent
    rounds: [1]
    status: open
    class: design
    severity: LOW
    fix: mechanical
  - key: summary-overstates-round-trip
    rounds: [1]
    status: open
    class: consistency
    severity: LOW
    fix: mechanical
---

Round 1 is the first round, so there is no earlier ledger.

- F1 maps to `parse-value-accepts-thousands-separators`: `Quantity.Parse` reads the value with default number styles, so `1,5 bar` becomes 15.
- F2 maps to `parse-non-numeric-value-untested`: no test reaches the non-numeric value path.
- F3 maps to `parse-culture-untested-host-dependent`: the current-culture reading is pinned by no test, and the parse tests depend on the host culture.
- F4 maps to `summary-overstates-round-trip`: the change description claims an exact round trip that `ToString` rounding does not give.

Each fix is local and its result is fixed by the finding text, so all four are `mechanical`.

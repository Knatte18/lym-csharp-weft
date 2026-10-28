---
round: 1
ledger:
  - key: empty-baseline-dir-crashes
    rounds: [1]
    status: open
    class: design
    severity: LOW
    fix: mechanical
  - key: culture-test-covers-one-case
    rounds: [1]
    status: open
    class: design
    severity: LOW
    fix: mechanical
  - key: machine-path-replacement-unpinned
    rounds: [1]
    status: open
    class: design
    severity: LOW
    fix: mechanical
  - key: test-private-fields-lack-underscore
    rounds: [1]
    status: open
    class: consistency
    severity: LOW
    fix: mechanical
  - key: machine-root-no-path-boundary
    rounds: [1]
    status: open
    class: design
    severity: NIT
    fix: mechanical
---

Round 1 raised five findings, none above LOW.
F1 maps to `empty-baseline-dir-crashes`, F2 to `culture-test-covers-one-case`, F3 to `machine-path-replacement-unpinned`, F4 to `test-private-fields-lack-underscore`, F5 to `machine-root-no-path-boundary`.
Each fix text names the result, so all five are mechanical.
There was no previous ledger, so nothing carries forward.

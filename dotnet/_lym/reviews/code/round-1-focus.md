---
round: 1
focus:
  - "ChurchillFriction.FrictionFactor: check the A, B and laminar terms against Churchill's 1977 equation (exponents 12, 16, -1.5, 1/12; constants 8, 7, 0.9, 0.27, 2.457, 37530), and whether relativeRoughness is validated like reynolds (negative or NaN input)."
  - "RegressionCases: confirm the diff only appends one case, leaves every existing case's entry unchanged, and that the new case really sits in the transitional regime for the 7.2 L/min 2-inch PVC water flow."
  - "ChurchillFrictionTests: check each Expected test bullet is met (64/Re in laminar, Colebrook within 1 % in turbulent), that the laminar tolerance and the Reynolds/roughness points cover the stated regimes, and that no test duplicates another existing friction model test."
  - "Commit messages: check each claim against the diff, in particular 'existing cases are unchanged' and 'transitional'."
---

Small three-file diff against a Goal-only decision record; the Goal's three Expected bullets are the measuring stick.

---
round: 1
focus:
  - "src/LyxCsharp.Hydraulics/HaalandFriction.cs: does the turbulent branch match the board entry's formula, 1/sqrt(f) = -1.8 log10((relativeRoughness/3.7)^1.11 + 6.9/Re), and the 2300 cut-off and guard of SwameeJainFriction?"
  - "HaalandFrictionTests.cs agreement cases: do all four cases actually stay within 3 percent of SwameeJainFriction, and does the smooth Re 100000 range check bracket the value near 0.018?"
  - "HaalandFrictionTests.cs against FrictionTests.cs: does any new test duplicate an existing test's coverage, or is any board-entry behavior (laminar, smooth turbulent, roughness, throw, agreement) left uncovered?"
  - "Doc comment on HaalandFriction: does it follow the conventions of the sibling SwameeJainFriction and the repository's constraints document?"
  - "_lyx/landing/summary.md: does every claim hold against the two-file diff?"
---

Diff is two new files; scope looks limited to the board entry's named files.

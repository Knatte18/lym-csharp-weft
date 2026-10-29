---
round: 1
focus:
  - "`ColebrookFriction.cs` iteration: does the relative-tolerance exit test and the `MaxIterations` cap match decision D2 and D3, and can the loop return an unconverged factor or run unbounded?"
  - "`ColebrookFriction.cs` against the sibling models: do the laminar branch, the input guard and the doc comments follow `HaalandFriction` and `SwameeJainFriction` (decision D1)?"
  - "`ColebrookFrictionTests.cs` `Slow` sweep: does it cover the grid decision D5 states (Re 4000 to 1e8, relative roughness 0 to 0.05), and does its floating-point loop counter reach the upper end of the range?"
  - "`ColebrookFrictionTests.cs` agreement test: does the grid and the 3 percent tolerance match decision D4, and does it hold for every listed case?"
  - "`ColebrookFrictionTests.cs` test economy: does `TurbulentFactorSolvesColebrookWhite` duplicate the residual assertion the `Slow` sweep already makes, and is every acceptance criterion in the decision record covered by a test?"
  - "Scope: does the diff touch only the two files the board entry's Scope lists, leaving `LymCsharp.Regression`, `LymCsharp.Units` and `LymCsharp.Reporting` alone?"
---

Seed focus from the board entry and decision record; the diff is two new files in one commit.

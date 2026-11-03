---
round: 1
focus:
  - "`src/LymCsharp.Hydraulics/PipeFlow.cs`, `Calculate` and `Reynolds`: does every `PipeFlowResult` field stay bit-identical to the pre-refactor inline code, given the floating-point operation order and that velocity is now derived twice?"
  - "`MeanVelocityMetresPerSecond`: do the unit conversion, the non-positive-flow check, the exception type and the `ParamName` match the old inline behavior for both `Calculate` and `Reynolds`?"
  - "`PipeFlowTests.ReynoldsMatchesFourRhoQOverPiMuD`: does it pin `Reynolds` for one laminar and one turbulent flow as the Expected section asks, and does it duplicate coverage already in the existing `Calculate` and `Classify` tests?"
  - "New doc comment on `Reynolds`: does it follow the repository's C# doc-comment conventions, and does the repository's constraints document require anything else (docs, changelog) for a new public API?"
  - "Scope: does the diff change anything beyond extracting `Reynolds` and making `Calculate` call it?"
---

Two files changed in a single commit; the decision record carries only the board entry under `## Goal`, so it is the measuring stick.

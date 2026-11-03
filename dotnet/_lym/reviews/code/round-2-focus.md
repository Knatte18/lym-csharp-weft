---
round: 2
focus:
  - "tests/LymCsharp.Hydraulics.Tests/PipeFlowTests.cs and src/LymCsharp.Hydraulics/PipeFlow.cs: does a test fail if PipeFlowCalculator stops routing Calculate through CachedFrictionModel (for example a call-counting IFrictionModel stub, two Calculate calls with the same pipe, fluid and flow above Re 2300, asserting one inner call), and does the PipeFlowCalculator doc comment describe the cached range, Re of 2300 and above, the way CachedFrictionModel's own doc does?"
---

Both items concern code a fix may have changed after the previous review; read them as the diff leaves them.

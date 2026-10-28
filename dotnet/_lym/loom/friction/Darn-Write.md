# Testing the regression harness with `lym gate test --wait`

The computed-scope gate ran tier0's `dotnet test LymCsharp.sln ...` in the worktree root (`run_dir` = `.../regression-harness`) instead of the `dotnet/` anchor, so it failed at once with `MSB1009: Project file does not exist`.
The hub's gate tiers set no `dir`, and the default run directory does not follow the anchor in a subdirectory.
Passing `-C <worktree>/dotnet` with an explicit target worked around it; I reported the problem to the parent, since the handoff's diff-scoped verify probably runs the same way.

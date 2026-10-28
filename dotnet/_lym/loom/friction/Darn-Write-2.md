# Re-verifying the regression harness after `lym loom resume`

The resumed spawn hit the same gate failure that halted the first one.
`lym gate test --wait` runs tier0's `dotnet test LymCsharp.sln` in the worktree root rather than the `dotnet/` anchor and fails with MSB1009.
The resume gave the spawn a fresh verify budget but left the gate config unchanged, so the re-spawn could not move forward.
Its prompt also named the prior work as `(none)` while the branch already held the first spawn's commit and an archived summary.

The code passes 82/82 when run with `-C <worktree>/dotnet`.
The run-directory problem is reported to the parent as a hub-wide gate setting.

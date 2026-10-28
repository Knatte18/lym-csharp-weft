loom halted blocked at Darn-Write

```
gate did not pass after 3 attempts; findings: /home/knatte/Code/lym-csharp-LYMHUB/regression-harness/dotnet/.lym/shed/regression-harness/gate-findings/Darn-Write.md; way forward: run "lym loom resume" in the task worktree, which re-spawns Darn-Write with a fresh verify budget
```

producer: Darn-Write
state: blocked
anomaly: escalation-to-human
history_entries: 4
trace_file: /home/knatte/Code/lym-csharp-LYMHUB/regression-harness/dotnet/.lym/logs/trace-20261028T093232Z-579775bd589eb14d-2289388.log

history:
- Preflight / done / 2026-10-28T09:32:32Z
- Drift-Baseline / skipped / 2026-10-28T09:32:32Z
- Drift-Await / skipped / 2026-10-28T09:32:32Z
- Darn-Write / stuck / 2026-10-28T09:57:10Z

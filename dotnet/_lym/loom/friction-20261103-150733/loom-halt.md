loom halted blocked at Drift-Await

```
drift: the baseline capture ended failed (drift: start the drift capture command in /home/knatte/Code/lym-csharp-LYMHUB/pipeflow-reynolds-helper/dotnet/dotnet: fork/exec /usr/bin/sh: no such file or directory), log /home/knatte/Code/lym-csharp-LYMHUB/pipeflow-reynolds-helper/dotnet/_lym/drift/logs/capture-20261103T142802.258Z.log; way forward: lym drift capture followed by lym loom resume, which captures again on the branch point and re-enters this row, or lym drift defer followed by lym loom resume, which goes on with regression deferred
```

producer: Drift-Await
state: blocked
anomaly: escalation-to-human
history_entries: 4
trace_file: /home/knatte/Code/lym-csharp-LYMHUB/pipeflow-reynolds-helper/dotnet/.lym/logs/trace-20261103T142802Z-d7f46928840ba4f6-3624042.log

history:
- Preflight / done / 2026-11-03T14:27:31Z
- Record-Write / done / 2026-11-03T14:27:31Z
- Drift-Baseline / done / 2026-11-03T14:28:02Z
- Drift-Await / stuck / 2026-11-03T14:28:02Z

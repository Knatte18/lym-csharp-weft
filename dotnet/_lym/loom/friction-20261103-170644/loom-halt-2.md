loom halted blocked at Drift-Await

```
drift: the baseline capture ended failed (exit 0 but wrote no file to /home/knatte/Code/lym-csharp-LYMHUB/report-significant-digits/dotnet/.lym/drift/baseline), log /home/knatte/Code/lym-csharp-LYMHUB/report-significant-digits/dotnet/_lym/drift/logs/capture-20261103T160453.725Z.log; way forward: lym drift capture followed by lym loom resume, which captures again on the branch point and re-enters this row, or lym drift defer followed by lym loom resume, which goes on with regression deferred
```

producer: Drift-Await
state: blocked
anomaly: escalation-to-human
history_entries: 6
trace_file: /home/knatte/Code/lym-csharp-LYMHUB/report-significant-digits/dotnet/.lym/logs/trace-20261103T160525Z-37a2579386b21ece-1951108.log

history:
- Preflight / done / 2026-11-03T16:04:09Z
- Record-Write / done / 2026-11-03T16:04:19Z
- Drift-Baseline / done / 2026-11-03T16:04:53Z
- Drift-Await / stuck / 2026-11-03T16:04:58Z
- Drift-Await / goto / 2026-11-03T16:05:25Z
- Drift-Await / stuck / 2026-11-03T16:05:30Z

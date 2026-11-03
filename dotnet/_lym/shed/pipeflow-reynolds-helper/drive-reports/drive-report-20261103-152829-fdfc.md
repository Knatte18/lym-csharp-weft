# Drive report for run `pipeflow-reynolds-helper`

- Written: 2026-11-03T14:28:29Z
- Position: history length 4, progress "blocked at Drift-Await"
- Reason: drift: the baseline capture ended failed (drift: start the drift capture command in /home/knatte/Code/lym-csharp-LYMHUB/pipeflow-reynolds-helper/dotnet/dotnet: fork/exec /usr/bin/sh: no such file or directory), log /home/knatte/Code/lym-csharp-LYMHUB/pipeflow-reynolds-helper/dotnet/_lym/drift/logs/capture-20261103T142802.258Z.log; way forward: lym drift capture followed by lym loom resume, which captures again on the branch point and re-enters this row, or lym drift defer followed by lym loom resume, which goes on with regression deferred
- Friction directory: /home/knatte/Code/lym-csharp-LYMHUB/pipeflow-reynolds-helper/dotnet/_lym/loom/friction
- Repair directory: /home/knatte/Code/lym-csharp-LYMHUB/pipeflow-reynolds-helper/dotnet/_lym/shed/pipeflow-reynolds-helper/drive-reports

## Rules applied

- no rule settles the stop: the tackler decides
- the tackler handed the stop back: The Drift-Await row stopped because the drift capture command could not start. The capture child ran in the working directory `dotnet/dotnet`, which does not exist (`ls` confirms; the real directory is `dotnet/`). Go reports a failed chdir as "fork/exec /usr/bin/sh: no such file or directory", so the error names the shell, but `/usr/bin/sh` exists now (symlink to dash). The shell path is not the cause. A plain re-launch would start in the same missing directory and fail again, so resume does not settle it. The fix is either to correct the directory the drift capture runs in, then `lym drift capture` and resume, or to defer regression for this run, which is a scope choice for the run's parent. I could not find where the capture directory is configured: no drift config in the repo's yaml, json or toml files.

## Attempts

- tackler attempt 1: verdict hand-back, verdict file /home/knatte/Code/lym-csharp-LYMHUB/pipeflow-reynolds-helper/dotnet/_lym/shed/pipeflow-reynolds-helper/tackler/attempt-1/verdict.yaml

## Question for the run's parent

The drift baseline capture runs in `dotnet/dotnet`, which does not exist. Which do you want: (1) Correct the capture directory to `dotnet/`, then run `lym drift capture` and `lym loom resume`, so regression is captured. (2) Run `lym drift defer` and `lym loom resume`, so the run goes on with regression deferred. Also, if you choose (1), name where the capture directory is configured, since I could not find it in the repo.

## Messages to the parent

- sent: Run pipeflow-reynolds-helper stopped with a question; stop report /home/knatte/Code/lym-csharp-LYMHUB/pipeflow-reynolds-helper/dotnet/_lym/shed/pipeflow-reynolds-helper/drive-reports/drive-report-20261103-152829-fdfc.md. Question: The drift baseline capture runs in `dotnet/dotnet`, which does not exist. Which do you want: (1) Correct the capture directory to `dotnet/`, then run `lym drift capture` and `lym loom resume`, so regression is captured. (2) Run `lym drift defer` and `lym loom resume`, so the run goes on with regression deferred. Also, if you choose (1), name where the capture directory is configured, since I could not find it in the repo. Answer briefly, then continue your task.

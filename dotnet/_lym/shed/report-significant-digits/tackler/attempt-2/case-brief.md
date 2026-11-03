## 1. Question

Why did the drift baseline capture exit 0 without writing `.lym/drift/baseline`, and should the run re-capture (`lym drift capture`, then `lym loom resume`) or defer the regression (`lym drift defer`, then `lym loom resume`)?

## 2. Facts

- Stop: run `report-significant-digits`, row `Drift-Await`, step 4 of 11, state `blocked`, `interrupt_policy: reinvoke`. `_lym/shed/report-significant-digits/drive-reports/drive-envelope-20261103-170535-f355.json`.
- Capture record: `.lym/drift/capture.json`. `state: failed`, `exit_code: 0`, `reason: exit 0 but wrote no file to .../.lym/drift/baseline`. Slot acquired and command started 16:04:53Z, ended 16:04:56Z (about 3 seconds). `head: 9017f2da38696ffab899e7ff08b3fb9c4ada5df9`.
- `.lym/drift/capture.json` skip list: `water-steel-dn100-swamee-jain`, `water-steel-dn100-haaland`, `oil-steel-dn100-laminar`, `water-pvc-2in-transitional`, `oil-steel-6in-turbulent`, `water-pvc-2in-transitional-churchill`. `.lym/drift/skip.txt` holds the same six.
- Capture logs `_lym/drift/logs/capture-20261103T160453.725Z.log` and `_lym/drift/logs/capture-child-20261103T160453.710Z.log`: both 0 bytes.
- `.lym/drift/baseline`: an empty directory, created 17:04.
- Step trace `.lym/shed/report-significant-digits/steps/37a2579386b21ece.trace.log` line 15: `shed: step done outcome=stuck state=blocked` at 17:05:35, with the same reason text. Its stderr log `.lym/shed/report-significant-digits/steps/37a2579386b21ece.stderr.log` is empty.
- Driver log `.lym/loom/driver.log` is empty.
- Commit at head: `9017f2d Add ChurchillFriction, valid in every flow regime`.

## 3. Ruled out

- A capture error that wrote a message: both capture logs and the step stderr are empty, so no message names the failure.
- A stale or conflicting stop: the envelope, the step trace and `capture.json` agree on the reason and the head.
- A baseline written to another path: `.lym/drift/baseline` is the only baseline path named, and it is empty.
- The skip list covering the whole suite: I read only the six names; I did not read the case list, so this is unverified.
- The cause is not named from what I read, so a plain re-launch does not settle it.

## 4. Hypothesis (unverified)

The capture child exited within about 3 seconds, before it opened its log or wrote the baseline. A likely reason is a skip filter that leaves no case to run, or a crash in the capture child before logging. The only skip entry for a case that is new to the recent commit is `water-pvc-2in-transitional-churchill`, so the commit `9017f2d` may be involved. This is a hypothesis, not a finding.

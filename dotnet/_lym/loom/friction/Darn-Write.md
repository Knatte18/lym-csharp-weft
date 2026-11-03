# Darn-Write for transitional-report-note: the shuttle prompt arrived late

The session's first message only asked it to load the scribe skills and reply `ok`; the shuttle prompt with the darn writer's instructions never followed.
The session went idle until the operator typed "fortsett?", then found the task through `_lym/shed/transitional-report-note/status.json` and the decision record, and made the change without these instructions.

The operator later pasted the path to `.lym/shuttle/<id>/prompt.md` by hand.
Only then did the session learn to check the board, commit, and write `_lym/landing/summary.md`.
It had also run `lym gate test` itself, which the prompt forbids.

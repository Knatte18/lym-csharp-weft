<!-- This is the decide step of the Bouncer's escalation brief for a round below its perch's hard cap.
     renderEscalation picks it over bouncer-fragment-decide-capped, fills its marker {{.slug}} and passes the result as the brief's decide_options marker, after the step's number.
lyx-stencil: sha256=fc0689e885c85109f34845958aceba6645907f5a316e48fd516e84abc46c9a67 build=444375fb9d2e2a0ad3f0a3db845ab3c3a50f634b time=2026-10-22T12:25:05Z -->

On the ground of the operator decision you applied, record the segment's outcome with exactly one of:
   - `lyx loom circling accept {{.slug}}` ends the segment unconverged on this round.
     The segment passes with its open findings, recorded as unconverged.
   - `lyx loom circling continue {{.slug}}` runs one more round.

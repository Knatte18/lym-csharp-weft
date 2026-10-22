<!-- This is the launch prompt for an adopted session: `lyx orch start --adopt` resumes an already-running Claude session as the orch strand, rendered by RenderAdoptPrompt (internal/orchengine/prompt.go).
     It must render to ONE line, so a resume without a launch prompt followed by a Send stays available.
     Its one marker is {{.role_path}}, the role file to read.
lyx-stencil: sha256=7b68c1d13ec004a6a081f48b21443ed04988f999d00d49e1f6fe6c85bb52713a build=444375fb9d2e2a0ad3f0a3db845ab3c3a50f634b time=2026-10-22T12:25:05Z -->
Read {{.role_path}} in full as your procedure as the hub orchestrator, then continue the work you were doing; the previous process's background shells and Monitors are gone and none is re-armed.

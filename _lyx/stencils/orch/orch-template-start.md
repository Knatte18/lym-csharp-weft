<!-- This is the fresh-launch prompt for the hub orchestrator session `lyx orch start` launches.
     It is read by RenderStartPrompt (internal/orchengine/prompt.go) and handed to shuttle as the launch prompt pointer, so it must render to ONE line.
     Its one marker is {{.role_path}}, the role file rendered from orch-template-role.
lyx-stencil: sha256=d995d65b249a6c61684fc40729368788452186468916b8ebd38b487e37940323 build=444375fb9d2e2a0ad3f0a3db845ab3c3a50f634b time=2026-10-22T12:25:05Z -->
Read {{.role_path}} in full as your procedure as the hub orchestrator, then read the board and continue.

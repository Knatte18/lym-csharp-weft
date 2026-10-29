<!-- This is the wake line the `lym orch` watcher types into the orch once a usage limit that stopped it should have reset, rendered by RenderUsageLimitWake (internal/orchengine/prompt.go).
     It must render to ONE line: shuttle's Send refuses multi-line text.
     Its one marker is {{.reset}}, when the limit reset, or that its reset time could not be read.
lym-stencil: sha256=a4ebcfd778d92ac10c303f6143d9037afc03c96e8de53c922d626db085144655 build=c49ba448cd942b81a5c9fe25553707a8279487d0 time=2026-10-29T10:06:41Z -->
[lym orch watcher] The usage limit that stopped this session {{.reset}}. Run `lym tender revive`, then check each run with `lym batten status <slug>` and restart what stopped.

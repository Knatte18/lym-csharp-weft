<!-- This is the shared edit directive: internal/editdirective.Directive renders it unconditionally into the edit_directive marker of every spawned role's opening stencil.
     It is the rule's only statement. It declares no marker and must stay marker-free.
lyx-stencil: sha256=bcaa21fe28d53daaa8f1e7ac801b3686f21c9ab103215be3745f32ab187aceb5 build=e6fbc14c73bc8e30b7b1320595d8b2dcfc066b8d time=2026-10-26T15:16:34Z -->

## Editing files

Change files with Edit or Write, or by script with `sed -i`, `perl -i`, `awk` or a shell heredoc or redirect wherever a script is simpler, such as a mechanical change across many sites.
A Go file's formatting is fixed with `lyx loom fmt <file>`.
Scratch files go under `.scratch/` in the working directory, never a system temp directory, and are removed by their literal path.

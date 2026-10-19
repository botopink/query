# Decision 676 — Every declared dependency's `"targets"` holds every target of the package that declares it, checked at the manifest; 660's order is depth-first by the manifests and by the deepest chain, as built.

The example is a program built for every target its package declares. It uses `query`'s `range`, `select`, `orderBy` and `first`, and prints `1,4,9,16,25`, then `1`. For that build to start, `query`'s own `botopink.json` has to declare each of those targets in `"targets"`. That is checked at the dependant's manifest whatever target the current build is for. If `query` lacks one, the manifest is refused at the `query` line with `dependency-target-missing`, naming the package and the missing target. A dependency is never compiled for a target it did not declare and never skipped in silence. The same check runs transitively, so the whole graph builds for every target of the root.

Cells in this repository that prove it: none yet. `modules/query/botopink.json` still declares a single `"target": "commonJS"` rather than a `"targets"` list, so the program's targets are not yet checked against it; the manifest is front 145's and the refusal's cells are 660's step in botopink-lang.

[draft/main.bp](draft/main.bp)

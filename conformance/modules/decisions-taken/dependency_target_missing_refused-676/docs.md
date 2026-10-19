# Decision 676 — Every declared dependency's `"targets"` holds every target of the package that declares it, checked at the manifest; 660's order is depth-first by the manifests and by the deepest chain, as built.

The example is the refused program. Its manifest, `draft/botopink.json`, declares the targets `erlang` and `commonJS` and depends on `query` and on `kit`, a package beside it (`draft/kit/`). `kit`'s own manifest declares only `commonJS`. The dependant's manifest is refused at the `kit` line of its `dependencies` with `dependency-target-missing`, naming the package and the missing target: `kit declares no erlang target`. That holds whatever target the current build is for, so a `--target commonJS` build is refused too, and nothing of `main` runs. A dependency is never compiled for a target it did not declare and never skipped in silence. The check is transitive, a dependency's dependencies being checked against its targets, so the whole graph builds for every target of the root. The fix is `kit` declaring `erlang` in its `"targets"`, or the dependant dropping it.

The same rule binds `query`: its `modules/query/botopink.json` still declares a single `"target": "commonJS"`, so it must declare every target a consumer builds for.

Cells in this repository that prove it: none yet. The refusal and its cells are 660's step in botopink-lang; `query`'s manifest is front 145's.

[draft/main.bp](draft/main.bp)
[draft/botopink.json](draft/botopink.json)
[draft/kit/botopink.json](draft/kit/botopink.json)
[draft/kit/src/kit.bp](draft/kit/src/kit.bp)

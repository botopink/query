# Decision 703 — `botopink.json`'s `files` is gone: what a consumer may import is the tree (every module under `src/` outside `src/internal/`, 592 (8)); once B-69 / B-75 land, a manifest still writing `files` is refused, located, naming `src/internal/`; `workspaces` stays (512).

The example is the refused program. Its manifest, `draft/botopink.json`, still writes `"files": ["main.bp", "orders.bp"]`. What a consumer may import is the tree instead: every module under `src/` outside `src/internal/`. The manifest is refused at the `files` key, located, and the message names `src/internal/` as the way to keep a module private. Nothing of the program builds. Without the key, the program is the same tree: `main` imports `orders.Order` and `orders.byCity` by path, and `byCity` groups the orders with `query`'s `groupBy`, printing `Lyon=2,Paris=1`. `workspaces` stays a key of the manifest.

`query`'s own `modules/query/botopink.json` and `modules/query-test/botopink.json` still write `files` (the reference's Departures section); they lose the key in front 145.

Cells in this repository that prove it: none yet. The refusal waits on botopink-lang's B-69 and B-75.

[draft/main.bp](draft/main.bp)
[draft/botopink.json](draft/botopink.json)
[draft/orders.bp](draft/orders.bp)

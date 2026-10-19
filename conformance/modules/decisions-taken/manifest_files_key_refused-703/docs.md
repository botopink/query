# Decision 703 — `botopink.json`'s `files` is gone: what a consumer may import is the tree (every module under `src/` outside `src/internal/`, 592 (8)); once B-69 / B-75 land, a manifest still writing `files` is refused, located, naming `src/internal/`; `workspaces` stays (512).

The example imports `of`, `Grouping`, `QueryTable` and `QueryColumn` from `query`. Each is reachable because its module sits in the package's tree under `src/` and outside `src/internal/`, not because the manifest lists a file. The program groups orders by city and prints `Lyon=2,Paris=1`, then builds a `QueryTable` and prints `orders.city_name`.

Once B-69 and B-75 land, a `botopink.json` that still writes `files` is refused, located, naming `src/internal/` as the way to keep a module private. `workspaces` stays.

Cells in this repository that prove it: none yet. `modules/query/botopink.json` and `modules/query-test/botopink.json` still write `files` (the reference's Departures section); they lose the key in front 145.

[draft/main.bp](draft/main.bp)

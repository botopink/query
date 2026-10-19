# Decision 284 — Configuration in JSON, case by case.

The example keeps its settings as its own data, a `val` list of `Setting` records. It queries them twice: the fluent layer picks the `page.` keys, and the template form `` query @`… limit 1` `` reads the title. It prints `page.size,page.title`, then `Orders`. The program's `botopink.json` holds what the build needs and nothing else: its name and the `query` dependency. `query` is not a `.bpp` package, so no manifest of it or of its consumers names a `"bpp"` package. A `.bpp` program names exactly one package there, `"bpp": "<package>"`, and that package's annotations find the roles.

Cells in this repository that prove it: none yet. The rule reaches `query` only through its consumers' manifests.

[draft/main.bp](draft/main.bp)

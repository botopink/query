# Decision 334 — A wasm build binds to the runtime it runs on; wasmtime with WASI preview 2 first.

The example is a program a wasm build runs as is: it sums the minutes of the open tasks with `query`'s `where` and `sum`, lists their names and builds a separator with `repeat`. It prints `60`, `test then ship` and `---`. A wasm build picks its host profile in the program's manifest, `"wasm": {"host": "wasi"}` (the default, wasmtime under WASI preview 2) or `"browser"` (JavaScript imports, node too). `query` binds no host cell, so one build serves either profile, and nothing in the library is ever `std-unsupported-on-target` for a host.

Cells in this repository that prove it: none yet. The library's wasm cells come with front 145's Conformance step.

[draft/main.bp](draft/main.bp)

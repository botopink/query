# Decision 594 — Every library runs at build on the wasm comptime runtime (434), jhonstart first; a function only Erlang or Node can run is a located error there.

The example runs `query` at build. `comptime bigNames()` filters, orders and projects a module-level list with the fluent layer while the program is being built, and splices the answer, `Lyon, Paris`, in as a literal. `atBuild == bigNames()` prints `true`, because the answer at build is the one at run. `comptime range(1, 5).aggregate(…)` folds `10` at build. The `` query @`…` `` template is itself code that runs at build, with its lexer and parser in botopink. The program prints `Lyon, Paris`, `true`, `10` and `Nice`. All of this runs on the wasm comptime runtime whatever `--target` the build uses, because every function `query` has is written in botopink, so each has a wasm form.

A library function with no wasm form (a host cell for Erlang or Node only, or a real host effect) reached from a `comptime` is refused at build, at the call, naming the cell. `query` has no host cell, so nothing of it is refused.

Cells in this repository that prove it: none yet. The library's step for the decision is a step of front 145.

[draft/main.bp](draft/main.bp)

# conformance

The `query` library's conformance tree (decisions 457, 463). [`docs.md`](docs.md) is the library's reference;
every example in it is a cell of `modules/`, checked by `botopink cell check conformance/`.

- `docs.md` — the reference, one section per public module, in the pattern of botopink-lang's `conformance/docs.md`:
  - *Loading* — the dependency, how `from "query"` resolves, the three ways a consumer reaches the library.
  - *The `query` module* — `Query<T>` and its operators, `Grouping<K, V>`, the constructors `of`, `empty`, `range`, `repeat`.
  - *The query string* — `` query @`…` ``: the grammar, the answer's shape, holes, aggregates, joins, the front-end, refusals.
  - *The database target* — `QueryContext<E>`, `QueryTable`, `QueryColumn` and what a persistence library supplies.
  - *The `query-test` package* — the library's testing member.
  - *Departures* — where the source still writes a form the rules in force have replaced.
- `modules/<cell>/` — one cell each (`loading_imports`, `generic_loader_binding`, `query_context_declared`); `modules/botopink.json`
  is the workspace whose members they are, declaring the shared path dependency on `../../modules/query`.
- `modules/decisions-taken/<slug>-<N>/` — front 166's decision cells (decision 651: the subtree is that front's alone): one
  per code-bearing row of `../decisions-taken.md`, a `docs.md` (first line generated) and, in phase 1, `draft/main.bp` with any
  sibling modules it needs (decision 649; no `src/`, no manifest, so `botopink cell check` finds no cell there); kept by
  `scripts/decision-cells.py` of the meta repository and its CI check 7.

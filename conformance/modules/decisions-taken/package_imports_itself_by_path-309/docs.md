# Decision 309 — Inside a package, an import of its own module is always by path, never `from`:

The example is a package with three modules besides `main`. `city.bp` declares `City`. `totals.bp` counts and summarises cities. `internal/names.bp` sits under `src/internal/`, so it belongs to the package and no consumer reaches it. Each module imports its siblings by path in braces: `import {city.City};`, `import {internal.names.names};`. Only the dependency is imported with `from`: `import {of} from "query";`. `main` uses the same form for everything of its own package, the internal module included (`import {internal.names};`, then `names.names(cities)`). It prints `2`, `Lyon, Nice, Paris` and `Lyon, Nice, Paris (2 big)`.

Writing the package's own name as the source, as in `import {totals.summary} from "<this package>";`, is refused with `module-import-with-from` at the source string, and the fix is the brace form. The rule has no exception for std, a bundled package or a dependency: inside `query`'s own sources the library reaches its modules by path, never `from "query"`.

Cells in this repository that prove it: none yet. `query` is a single-module library today (`modules/query/src/query.bp`), so the package has no sibling module to import; its feature cells come with front 145's Conformance step (decision 465).

[draft/main.bp](draft/main.bp)
[draft/city.bp](draft/city.bp)
[draft/internal/names.bp](draft/internal/names.bp)
[draft/totals.bp](draft/totals.bp)

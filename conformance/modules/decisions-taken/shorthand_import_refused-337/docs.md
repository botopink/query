# Decision 337 — The shorthand import goes; `mod m;` binds the namespace `m` in the module that declares it.

The example totals a list of orders. `draft/totals.bp` is a module of the package. It declares `Order`, `cityTotal` and `busiest` as `pub`, and reads them with `query`'s `where`, `sum` and `orderByDescending`. `main` imports by the module's path in two ways. `import {totals.Order, totals.cityTotal};` names the module, then the item, with no `from`. `import {totals};` imports the module as a namespace and reads `totals.busiest(…)` through it. The library comes `from "query"`, std `from "std"`. The program prints `16`, `Lyon`, then `none` for no orders.

The refused spelling is the shorthand. `import {cityTotal};` has no `from`, and its first segment names no module of the package, so it is refused with `shorthand-import` at the item. Only `totals` declares `cityTotal` `pub`, so the error gives the fix, `write import {totals.cityTotal};`, which is the line the example uses. A bare `import {of};` does not reach the library either, even though `query` declares `of`; it has to be written `from "query"`.

337 (2)'s `mod m;` namespace and 337 (3) are replaced by decision 592 (1): every `.bp` under `src/` is a module, its path its name, and there is no `mod` / `pub mod`, no combined form and no re-export syntax. The library's own `modules/query/src/root.bp` still declares `pub default mod query;`, which goes with the codemod of decision 592 over the library in front 145.

Cells in this repository that prove it: none yet. The library is one module today, so it has no shorthand import to migrate; its feature cells come with front 145's Conformance step (decision 465).

[draft/main.bp](draft/main.bp)
[draft/totals.bp](draft/totals.bp)

# Decision 206 — `import {…} from "<name>"` resolves only to a package — std, a bundled package or a declared dependency.

The example has a module of its own named `query` (`draft/query.bp`, which holds `describe`), and the library it depends on is also called `query`, so each import line says which one it means. `import {of} from "query";` names a package, so it reaches the library and never the module beside `main`, even though that module has the same name. `import {query.describe};` is the brace form with no `from`: the module's path, then the item, so it reaches this package's own `query.bp`. Every `.bp` file of the package is a module named by its path, so nothing declares it. `io.terminal` comes `from "std"`, which is a package like `query`.

`main` counts the cities of at least five with the library's `countWhere` and prints the count through the package's `describe`: `big cities: 2`, then `all cities: 3`. Had the example written `import {describe} from "query";`, the import would look for `describe` in the library, which does not declare it. Using a module of this package as the source of a `from` is refused with `module-import-with-from` at the source string, and the fix is the brace form the example uses.

Cells in this repository that prove it: `modules/loading_imports` and `modules/generic_loader_binding` (`from "query"` reaching the library off disk). None yet has a module of the consumer's own; that one comes with front 145's Conformance step (decision 465).

[draft/main.bp](draft/main.bp)
[draft/query.bp](draft/query.bp)

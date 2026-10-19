# Decision 330 — `?T` is read with TypeScript's operators and has no methods; `result` goes; a type declared in a type is its associated type.

The example reads a census through `query`'s optional terminals. `first()`, `last()` and `max(…)` answer `?T`, and every optional is read with an operator, never a method. `Entry` is declared in the body of `Census`, so it is `Census`'s associated type and is written `Census.Entry` in signatures, constructors and array types. `Census.biggest` answers `?Census.Entry`. `top?.name ?? "nobody"` reads a member only when the entry is there. `top?.mayor` reads a member that is itself `?string`, and the result is `?string`, not `??string`, because `?.` flattens. `sorted?.[1]` indexes an optional array. `picker(true)?.(…)` calls an optional function value, and with `picker(false)` the call is skipped and `??` supplies `no picker`. `top!.pop` takes the entry when the program knows it is present; a `null` there stops the program with the location and the expression, the same on every target. The parentheses in `(… ?? 0) > 5 || top == null` are required, because `??` beside `||` without them is a compile error.

`parseMin` answers a `Result`, and the program reads it with that type's own methods (`isOk()`, `unwrapOr(0)`, `isError()`); there is no `result` namespace. The program prints `Paris`, `no mayor`, `nobody`, `Nice`, `Nice`, `no picker`, `9`, `true`, `true`, `2` and `true`.

Cells in this repository that prove it: none yet. The library already answers `?T` from its element terminals and `min` / `max`; its feature cells come with front 145's Conformance step (decision 465).

[draft/main.bp](draft/main.bp)

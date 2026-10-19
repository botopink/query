# Decision 622 — Every function writes its return type, `-> void` included: `fn m(self: Self) -> void { … }`, `pub fn main() -> void { … }`; a `fn` with no `->` is refused at its name, `fn-return-missing`, `write -> void, or the type it answers`.

Every function in the example writes its return type, whatever its form. `Ledger.rows` answers `Query<Order>`. `Ledger.report` prints, so it is `-> void`. The private method `totalOf` answers `i32`. The free functions `label` and `printAll` declare their types too. The typed lambda `fn(order: Order) -> bool { … }` writes its return like a declaration, and `main` is `pub fn main() -> void`. The short lambdas handed to `query`'s operators (`{ o -> o.total }`) are not `fn` forms, so they have nothing to write. The program prints `Lyon=16`, `Nice=0`, `Lyon:10` and `Lyon:6`.

A `fn` written without `->` is refused at its name with `fn-return-missing` and the fix `write -> void, or the type it answers`. That applies to a free function, a method and `main` alike. A valued tail or `return` in a `-> void` body is refused at the value (409).

Cells in this repository that prove it: none yet. The library's own sources still have functions without `->` (24, by decision 622's count), and the codemod over them is front 145's; the cells come with its Conformance step.

[draft/main.bp](draft/main.bp)

# Decision 668 — A function's type parameter is always bound by a parameter, never by the return type alone.

The example is the refused program. `nothing<T>() -> ?T` declares a type parameter that occurs only in its return type. No argument fixes `T`, only the caller's wish (`val missing: ?City = nothing();`), and the body cannot act on it. The declaration is refused at `nothing` with `type-parameter-unbound`, naming `T`, and nothing of the program runs.

A type parameter is bound when it occurs in a parameter's type: directly (`x: T`), inside one (`q: Query<T>`, `key: fn(item: T) -> K`), or as the type token `comptime t: type<T>`. The fix is one of those, as in `nothing<T>(comptime t: type<T>) -> ?T`, called `nothing(City)`, or an answer of `unknown`. `query` declares three such functions itself: `empty<T>() -> Query<T>`, the template `query<T>(syntax q: string) -> T`, and `QueryContext`'s `run<T>`. Each gains a binding parameter in front 145.

Cells in this repository that prove it: none yet. The refusal and its cells are botopink-lang's step B-152; `query`'s three sites migrate in front 145.

[draft/main.bp](draft/main.bp)

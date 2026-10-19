# Decision 668 — A function's type parameter is always bound by a parameter, never by the return type alone.

Each generic function in the example binds its type parameters through its parameters, so the arguments fix them. In `firstOr<T>(q: Query<T>, fallback: T)` the parameter `q` binds `T`. In `keys<T, K>`, `K` is bound inside the key function's type `fn(item: T) -> K`. `nothing<T>(comptime t: type<T>)` takes the type token, so the caller names the type once, `nothing(City)`, and the empty `Query<City>` follows from it. The program prints `Lyon`, `none`, `2` and `0`.

A function whose type parameter appears only in its return type is refused at the declaration with `type-parameter-unbound`, naming the parameter. Nothing at the call fixes it but the caller's wish. `query` declares three such functions: `empty<T>() -> Query<T>`, the template `query<T>(syntax q: string) -> T`, and `QueryContext`'s `run<T>`. Each gains a parameter that binds it (as `nothing` does here) or answers `unknown`, in front 145.

Cells in this repository that prove it: none yet. The cells come with front 145's migration of the three sites.

[draft/main.bp](draft/main.bp)

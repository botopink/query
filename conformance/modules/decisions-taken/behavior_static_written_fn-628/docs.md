# Decision 628 — In a `behavior`, a member without `self` is a static of the behavior and is written `fn`, never `default fn`: `fn unit() -> Self { … }` with a body is the behavior's own function, called `Shape.unit()`; `fn make() -> Self;` without a body is a static each implementor provides (626's `Configurable`); `default fn` is an instance member with an overridable default body, and nothing else.

`Ranked` has all four kinds of member. `rank` is an instance member with no body, which each implementor writes. `above` is a `default fn`: an instance member with a body an implementor may override. `floor` has no `self`, so it is a static of the behavior and is written `fn` with its body; it is the behavior's own function, called `Ranked.floor()`, and no implementor overrides it. `placeholder` is a static with no body, so each implementor provides it: `City.placeholder()`. The program keeps the cities ranked above `Ranked.floor()` with `query`'s `where`, orders them with `orderByDescending` and prints `Paris, Lyon`, then the placeholder's `?`.

Writing `default fn floor() -> i32 { … }` is refused at the modifier with `default-on-static` (`a member without self is a static: write fn`).

Cells in this repository that prove it: none yet. The library has one `default` member without `self` to respell (628's count), in front 145; the cells come with its Conformance step.

[draft/main.bp](draft/main.bp)

# Decision 738 — (c), a third option: neither `Pair<A, B>` nor `Function` exists — a pair is the tuple `#(A, B)`, and the function combinators are plain functions of a std module.

The example builds its pairs as tuples. `query`'s `zip` combines two queries index by index, and its lambda answers `#(name, pop)`, so the rows are `#(string, i32)[]`. A pair is read by position (`first._0`, `first._1`) or by destructuring (`val #(name, pop) = …`). The function combinator is a plain function of std's `functions` module: `compose(double, show)` applies `double`, then `show`, and the composed function is handed to `query`'s `select`. The program prints `Lyon 5`, `Paris 9` and `#10,#18,#6`.

There is no `Pair` and no `Function` in the prelude. `Pair.of(1, "x")` is an unknown name, and the error names the tuple.

Cells in this repository that prove it: none yet. The library's own `Pair.of` sites are respelled in front 145 (738 names `query.bp`).

[draft/main.bp](draft/main.bp)

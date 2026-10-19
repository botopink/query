# Decision 641 — (b) for the names, and no contract with one implementor: every primitive type is written lowercase — `i32`, `i64`, `u32`, `u64`, `f32`, `f64`, `bool`, `string`, `bigint`, `bytes`, `array<T>` — and a capitalised name is a contract shared by several types (`Number`, `Integer`, `Signed`, `Float`); `String`, `Bool`, `BigInt`, `Bytes` and `Array` dissolve into their types, which hold their methods and statics.

Every primitive type in the example is written lowercase: the fields `string`, `f64`, `i32` and `bool`, and the array as `array<Reading>` in `readings`' signature and as `array<string>` for `names`, with `string[]` as its other spelling in `sensors`. `query`'s `average` answers `f64` and `aggregate` folds an `i32`. A primitive's static belongs to its type, so it is called on the type: `string.fromCodepoint(65)` is `A`, and `array.range(0, 4)` hands `query` the run `0..3`, whose sum is `6`. The program prints `2.25`, `10`, `a,b`, `A` and `6`.

A capitalised name is a contract shared by several types (`Number`, `Integer`, `Float`), never a primitive's other name. `String`, `Bool`, `BigInt`, `Bytes` and `Array` name nothing, so `Array<Reading>` is an unknown type.

Cells in this repository that prove it: none yet. The library still writes `Array<T>` throughout `query.bp` (its reference's Departures section); the codemod is front 145's.

[draft/main.bp](draft/main.bp)

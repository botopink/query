# Decision 428 — erika's aggregates keep the strict reading and gain only forms that mean something, with one answer in memory and on the database.

The example reads each aggregate with the type the strict reading gives it. `count(*)` is `i32`. `sum(total)` over the `i32` field is `i64`, and over the empty `none` it is `0`. `avg(total)` is `?f64`, `6.75` here and `null` over no rows. `min(total)` and `max(total)` are `?i32`, read together through a tuple. `count(coupon)` over the `?string` field counts the rows where it is not null, which is `2`. With `group by city`, only the group field and aggregates are selected and `order by` names the group, so each row is `#(city, count, sum)`. The program prints `4`, `27`, `0`, `6.75`, `-1.0`, `4..10`, `2`, then `Lyon 2 16`, `Nice 1 7` and `Paris 1 4`. The answers are the same in memory and on a database, whose SQL carries `coalesce(sum(x), 0)`.

These are refused: an aggregate without `group by` together with `order by` or `limit` (`limit has nothing to cut`), a field beside an aggregate with no `group by` on it, `select *` with `group by`, and `count(f)` over a field that is not optional (the error names `count(*)`).

Cells in this repository that prove it: none yet. The repository's `refusals/` projects (`aggregate_beside_field`, `aggregate_order_by`, `star_group_by`, `count_field`) hold the refusals as projects, not cells. The expansion still follows the fluent layer (`sum` over `i32`, `average` over `f64`, `count(f)` refused on every field), so the strict types are front 145's.

[draft/main.bp](draft/main.bp)

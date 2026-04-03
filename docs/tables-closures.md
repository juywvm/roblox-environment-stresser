# Tables & Closures

Tests for table behavior, closures, upvalue semantics, table library functions, and all the corner cases that arise when tables are used as objects, arrays, or both simultaneously.

---

## Test 21 — `Upvalue Triangulation (Shared State)`

Creates two closures sharing the same upvalue. One increments the value, the other reads it. After 3 increments, reads the value and checks it's `3`.

**Fails when:** the two closures don't share the same upvalue slot (each has its own copy).

---

## Test 23 — `Cyclic Reference Handling`

Creates a table `t` where `t.self = t`. Verifies `t.self == t` and `t.self.self == t`. Then verifies `tostring(t)` doesn't loop infinitely.

**Fails when:** accessing a cyclic reference causes infinite recursion or the executor crashes.

---

## Test 29 — `Anonymous Recursion (Y-Combinator)`

Implements the Y combinator in Lua using closures. Uses it to build a recursive factorial without a named function. Tests `fact(5) == 120`.

**Fails when:** the Y combinator doesn't work, indicating broken closure or upvalue behavior.

---

## Test 30 — `Logic Short-Circuit & Side Effects`

Uses `and` / `or` with side-effectful functions. Verifies that the right-hand side is **not** evaluated when the short-circuit condition is met.

**Fails when:** both sides of `and`/`or` are always evaluated.

---

## Test 32 — `Luau table.clone Optimization`

Calls `table.clone` on a table with mixed keys. Checks all keys are present in the clone. Verifies the clone is a shallow copy — modifying the clone doesn't affect the original.

**Fails when:** `table.clone` is missing, or the clone shares mutable state with the original.

---

## Test 35 — `Function Argument Truncation`

Calls a function defined to take 2 arguments with 5 arguments. Verifies the first two are correct and the extras are silently dropped.

**Fails when:** extra arguments cause an error, or somehow corrupt the first two.

---

## Test 45 — `table.foreach Early Exit Logic`

Uses `table.foreach` (deprecated but present) with a callback that returns a value after 3 iterations. Verifies iteration stops at that point.

**Fails when:** `table.foreach` doesn't stop when the callback returns a non-nil value.

---

## Test 48 — `Table Key Identity (1 vs 1.0)`

Stores a value at key `1` (integer) and reads it at key `1.0` (float). In Lua, `1 == 1.0` and both should map to the same key.

**Fails when:** integer and float keys are treated differently.

---

## Test 59 — `Table Destruction during Iteration`

Creates a table with 5 keys. During iteration with `pairs`, removes some keys. Verifies the executor doesn't crash (behavior is undefined but shouldn't be a hard crash).

---

## Test 65 — `Closure Instantiation Identity`

Creates 10 closures in a loop. Verifies each is a distinct function object (`fns[1] ~= fns[2]`). Verifies each captures the correct loop variable.

**Fails when:** loop closures are deduplicated into one object, or they share the wrong upvalue.

---

## Test 73 — `Table Packing Logic (table.pack)`

Calls `table.pack(1, nil, 3)` and checks `packed.n == 3`, `packed[1] == 1`, `packed[2] == nil`, `packed[3] == 3`. The `.n` field must count all slots including nils.

**Fails when:** `table.pack` is missing, or `.n` doesn't count nil slots.

---

## Test 75 — `Luau table.create Allocation`

Calls `table.create(10000, 0)`. Verifies `#t == 10000` and spot-checks `t[1]`, `t[5000]`, and `t[10000]` are all `0`.

**Fails when:** `table.create` is missing, or the size is wrong.

---

## Test 76 — `Luau table.move Logic`

Tests `table.move` with overlapping source and destination. Moves indices 1-3 to starting at index 3 in the same table. Checks the copied values are correct without corruption.

**Fails when:** overlapping move corrupts values.

---

## Test 85 — `Dynamic Upvalue Mutation`

Uses `debug.setupvalue` to change an upvalue inside a running closure from outside. Calls the closure before and after mutation. Checks the value changed.

**Fails when:** `debug.setupvalue` is missing, or the mutation doesn't affect future calls.

---

## Test 90 — `Table Sort Custom Comparator`

Sorts a table in descending order with a custom comparator. Verifies `t[1]` is the largest and `t[#t]` is the smallest.

**Fails when:** the custom comparator is ignored.

---

## Test 98 — `Table Freeze & Rawset (Luau)`

Freezes a table with `table.freeze`. Tries a normal write — expects an error. Then tries `rawset` — checks behavior (Luau: `rawset` also errors on frozen tables).

**Fails when:** writes to a frozen table succeed without error.

---

## Test 105 — `Table Sort Comparator Fingerprint`

Uses a comparator that logs the number of comparisons made during a sort of 100 elements. Checks the number of comparisons is within reasonable bounds (not too many, not zero).

**Warns when:** comparisons are far outside expected bounds.

---

## Test 209 — `Closure Identity in Loops`

Creates 3 closures in a loop — `fns[1]`, `fns[2]`, `fns[3]`. Checks `fns[1] ~= fns[2]` (different objects). Checks `fns[1]() == 1` and `fns[3]() == 3` (correct upvalue capture).

**Fails when:** closures share the same object or capture the wrong value.

---

## Test 210 — `Shared Upvalue Mutation Across Closures`

A factory creates two closures — one increments a shared local, one reads it. After 3 increments, the reader should return `3`.

**Fails when:** each closure has its own copy of the upvalue rather than a shared slot.

---

## Test 241 — `Table.freeze Shallow Guarantee`

Freezes a table containing a nested table. Verifies writing to the outer table errors. Then mutates the inner table — this should succeed (freeze is shallow).

**Fails when:** `table.freeze` recursively freezes children.

---

## Test 242 — `Table.clone Metatable Non-Copy`

Clones a table that has a metatable. Checks that the clone's content is correct, and that `getmetatable(clone) == mt` (the same metatable reference is preserved).

**Fails when:** `table.clone` strips the metatable or deep-copies it.

---

## Test 243 — `Sparse Array Length Boundary`

Creates `{1, 2, nil, 4, nil, nil, 7}`. Checks `type(#t) == "number"` and `#t >= 2`. The exact value is implementation-defined, but the operator must return a number.

**Fails when:** `#` throws an error or returns a non-number.

---

## Test 244 — `Table.move Overlapping Ranges`

Moves elements `{1,2,3,4,5}[1..3]` to position 3. Checks `t[3]==1`, `t[4]==2`, `t[5]==3`.

**Fails when:** overlapping ranges cause corruption.

---

## Test 245 — `Frozen Table Chain`

Freezes an inner table then an outer table containing it. Checks both are frozen and writes to both fail.

**Fails when:** `table.isfrozen` misreports, or writes succeed.

---

## Test 246 — `Table.create Large Allocation`

Creates a table with 10,000 slots pre-filled with `0`. Checks length and spot-checks values.

**Fails when:** `table.create` is broken or length is wrong.

---

## Test 278 — `Table.find Value Location`

Calls `table.find({"a","b","c","d"}, "c")` — expects `3`. Calls with `"z"` — expects `nil`. Tests `start` argument: `table.find(t, "b", 3)` should return `nil` (starts searching after `"b"`).

**Fails when:** `table.find` is missing, finds wrong index, or ignores start offset.

---

## Test 279 — `Table.find on Frozen Table`

Calls `table.find` on a frozen table. Searching should still work on read-only tables.

**Fails when:** `table.find` errors on frozen tables.

---

## Test 293 — `Recursive Table Tostring Safety`

Creates `t.self = t`. Calls `tostring(t)`. Expects a string without infinite loop.

**Fails when:** `tostring` on a cyclic table hangs the executor.

---

## Test 295 — `Table Pass-by-Reference`

Creates `{x = 0}`, passes it to a function that sets `x = 42`. Checks the original table has `x == 42`.

**Fails when:** the table was copied instead of passed by reference.

---

## Test 303 — `Raw Functions Complete Check`

Creates a table with `__index`, `__newindex`, and `__len`. Checks:
- `rawget` bypasses `__index`
- `rawset` bypasses `__newindex`
- `rawequal` bypasses `__eq`
- `rawlen` bypasses `__len`

**Fails when:** any raw function doesn't bypass its corresponding metamethod.

---

## Test 307 — `Weak Table Mode KV`

Creates `{__mode = "kv"}` table. Stores key and value. Checks initial access works.

**Fails when:** the table setup fails.

---

## Test 308 — `Table.sort Equal Element Stability`

Sorts elements with equal sort keys. Checks the resulting order is consistent (ascending in the sort key).

**Fails when:** table.sort violates ordering for equal elements.

---

## Test 309 — `Table.concat Range and Separator`

Calls `table.concat({"a","b","c","d","e"}, "-", 2, 4)`. Expects `"b-c-d"`.

**Fails when:** range or separator is ignored.

---

## Test 338 — `Table.unpack Multi Return`

Unpacks `{10,20,30}` and checks `a,b,c == 10,20,30`. Also tests range: `table.unpack({1,2,3,4,5}, 2, 3)` returns `2,3`.

**Fails when:** unpack doesn't handle ranges correctly.

---

## Test 341 — `Table.isfrozen Detection`

Checks `table.isfrozen({}) == false`. After `table.freeze`, checks `table.isfrozen == true`.

**Fails when:** `table.isfrozen` misreports state.

---

## Test 344 — `Rawlen String and Table`

Checks `rawlen("Hello") == 5` and `rawlen({1,2,3}) == 3`.

**Fails when:** `rawlen` returns wrong values.

---

## Test 364 — `Ipairs Stops at First Nil`

Creates `{1, 2, nil, 4, 5}`. Iterates with `ipairs`. Checks count is `2` (stops at `nil`).

**Fails when:** `ipairs` continues past `nil`.

---

## Test 365 — `Pairs Hash Key Iteration`

Creates `{a=1, b=2, c=3}`. Iterates with `pairs`. Checks all three keys are found.

**Fails when:** `pairs` misses hash-part keys.

---

## Test 369 — `Table.sort Custom Comparator`

Sorts `{5,3,1,4,2}` in descending order. Checks `t[1]==5`, `t[5]==1`.

**Fails when:** custom comparator is ignored.

---

## Test 389 — `Deeply Nested Table Access (100 Levels)`

Builds a 100-level deep nested table structure. Sets a value at the deepest level. Traverses to it and reads the value.

**Fails when:** deep traversal causes a stack overflow.

---

## Test 390 — `Massive Table Iteration (10K)`

Creates a sequential table with 10,000 entries. Iterates with `ipairs` and counts. Checks count is 10,000.

**Fails when:** iteration stops early.

---

## Test 399 — `Table.pack N Field`

Packs `(1, nil, 3)`. Checks `.n == 3`, positions 1 and 3 have correct values, position 2 is nil.

**Fails when:** `.n` doesn't count nil slots.

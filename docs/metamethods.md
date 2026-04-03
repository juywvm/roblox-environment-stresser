# Metamethods & Metatable Behavior

These tests verify that Lua's metamethod system works correctly and that the executor hasn't broken, bypassed, or reordered any metamethod dispatch logic.

---

## Test 22 — `Newproxy & Metatable Inheritance`

Creates a userdata via `newproxy(true)`, sets a metatable on it with a custom `__index`, and verifies the lookup works. Also confirms `getmetatable` returns the table — not `nil` and not a string guard.

**Fails when:** `newproxy` doesn't pass a writable metatable, or `__index` dispatch is broken.

---

## Test 37 — `Yield Across Metamethod (__call)`

Sets up a `__call` metamethod that yields inside a coroutine. Verifies that yielding from within a `__call` invocation works correctly, and the value propagates back after resume.

**Fails when:** the executor prevents yields inside metamethod dispatch.

---

## Test 42 — `Weak Table Metadata (__mode)`

Creates a table with `{__mode = "v"}` (weak values). Stores a table inside it, removes all strong references, forces a GC, and checks if the weak value was collected. Also verifies `__mode = "k"`.

**Fails when:** weak tables never collect entries (GC is disabled or broken in the executor).

---

## Test 52 — `Metatable __call Chaining`

Creates a callable table (has `__call`). Inside the `__call` handler, returns another callable table. Verifies that chaining `obj()()` works and both metamethods fire in order.

**Fails when:** the second `__call` isn't dispatched or the chain returns wrong values.

---

## Test 62 — `Locked Metatable Simulation`

Creates a table with `__metatable = "locked"` set manually. Calls `getmetatable` on it and verifies it returns the string `"locked"` instead of the actual metatable. Then tries to call `setmetatable` on it and expects an error.

**Fails when:** `getmetatable` returns the real table instead of the guard string, or `setmetatable` doesn't error.

---

## Test 64 — `Metatable Hierarchy (No Inheritance)`

Confirms that metatables are not inherited. Creates a parent table with a metatable, creates a child table pointing to the parent via `__index`, and sets a *different* metatable on the child. The child's metamethods should use its own metatable, not the parent's.

**Fails when:** the child inherits the parent's metamethods through `__index` when it has its own metatable.

---

## Test 67 — `Metatable __index Function Protocol`

Sets `__index` to a function. Verifies:
- The function receives `(table, key)` as arguments
- Keys that exist directly on the table don't trigger `__index`
- Only missing keys trigger it

**Fails when:** `__index` is bypassed, or called even for keys that exist.

---

## Test 80 — `Metatable __newindex Interception`

Sets `__newindex` to a function that logs writes. Writes to the table and checks the write was intercepted. Then uses `rawset` and verifies it bypasses `__newindex`.

**Fails when:** `__newindex` doesn't fire on write, or `rawset` still triggers it.

---

## Test 82 — `Metatable __eq vs rawequal`

Sets `__eq` on two tables to return `true`. Verifies `a == b` returns `true` (metamethod active). Then verifies `rawequal(a, b)` returns `false` (bypasses the metamethod, they're different objects).

**Fails when:** `rawequal` honors the `__eq` metamethod, or `==` ignores it.

---

## Test 83 — `Length Operator (#) Dynamic`

Sets `__len` on a table. Verifies `#table` calls the metamethod. Also checks that `rawlen` bypasses `__len` and returns the actual array length.

**Fails when:** `#` doesn't call the metamethod, or `rawlen` uses the metamethod.

---

## Test 103 — `Corrupted Userdata Metatable`

Creates a userdata with `newproxy(true)`, sets a broken `__index` (one that errors), and wraps the access in `pcall`. The error should be caught — the executor must not crash or freeze.

**Fails when:** the error from the broken `__index` is not catchable, or the executor crashes entirely.

---

## Test 111 — `Table Metamethod __len Bypass`

Tests that `rawlen` bypasses a `__len` metamethod. Similar to Test 83, but focuses specifically on confirming the bypass is complete — `rawlen` must never invoke `__len`.

**Fails when:** `rawlen` dispatches through `__len`.

---

## Test 119 — `Userdata Metatable Locking (__metatable)`

Sets `__metatable` to a string on a `newproxy` userdata. Then tries to call `setmetatable` on it — expects an error. Also verifies `getmetatable` returns the guard string.

**Fails when:** the guard is ignored.

---

## Test 160 — `Triple __index Chain Resolution`

Creates three tables — `c`, `b`, `a` — with `a.__index = b` and `b.__index = c`. Sets a value only in `c`. Reads it from `a` and expects it to resolve through the chain. Then removes the value from `c` and verifies `a.key` becomes `nil`.

**Fails when:** the chain doesn't propagate, or stale values are cached.

---

## Test 161 — `__newindex vs rawset Bypass`

Writes to a table with `__newindex`. Checks the write was intercepted by the handler (the value wasn't stored in the table directly). Then writes with `rawset` and checks the value *is* stored directly.

**Fails when:** `rawset` still triggers `__newindex`, or `__newindex` doesn't intercept.

---

## Test 162 — `__concat Metamethod on Tables`

Defines `__concat` on two tables. Does `a .. b` and verifies the result is a string produced by the metamethod. Both left and right operands must trigger it.

**Fails when:** `..` doesn't dispatch through `__concat` on tables.

---

## Test 163 — `__unm Metamethod on Table`

Defines `__unm` on a table. Applies unary minus (`-obj`) and checks the result equals `-self.value`. Must be called with the object as the single argument.

**Fails when:** `-obj` doesn't call `__unm`, or the argument is wrong.

---

## Test 164 — `__mod Metamethod Custom Logic`

Defines `__mod` to multiply two values instead of computing modulo. Does `a % b` and checks the result is `a.v * b.v`. This ensures `%` dispatches through `__mod` and doesn't just use built-in math.

**Fails when:** `%` ignores `__mod` and computes real modulo.

---

## Test 165 — `__pow Metamethod Precision`

Defines `__pow` using `math.pow`. Raises a value to the power of 0.5 (square root). Checks the result against `math.sqrt` with a tolerance of `1e-10`.

**Fails when:** `^` ignores `__pow`, or the precision is off.

---

## Test 166 — `__lt vs __le Independence`

Defines both `__lt` and `__le` on tables. Checks that `a < b` uses `__lt` and `a <= b` uses `__le` — they must be dispatched independently, not derived from each other.

**Fails when:** `<=` is derived from `not (b < a)` instead of calling `__le`.

---

## Test 167 — `__index Returns Callable Table`

`__index` returns an inner table that itself has `__call`. Calls `obj.anything()` and verifies the inner `__call` fires. Tests chaining of `__index` and `__call` in a single access.

**Fails when:** the result of `__index` isn't dispatched through `__call`.

---

## Test 168 — `__call Vararg Forwarding`

Defines `__call` to receive varargs and return the count plus select results. Calls with `("A", nil, "C")` and checks all three arguments arrive correctly, including the `nil` in the middle.

**Fails when:** varargs are truncated at the `nil` or the count is wrong.

---

## Test 169 — `__tostring Non-String Return`

Defines `__tostring` to return a number (`42`) instead of a string. Calls `tostring(obj)` and checks whether this causes an error or gets coerced. In standard Luau, returning a non-string from `__tostring` should error.

**Fails when:** `tostring` accepts numbers from `__tostring` without complaint.

---

## Test 170 — `__index Table vs Function Priority`

First sets `__index` to a table fallback and verifies a key is found through it. Then replaces `__index` with a function and verifies the same key now goes through the function instead.

**Fails when:** swapping `__index` from table to function (or vice versa) doesn't take effect.

---

## Test 171 — `Shared Metatable Propagation`

Two objects share the same metatable. Mutates a field inside the metatable's `__index` table. Verifies both objects see the new value immediately without any staleness.

**Fails when:** one object sees a cached old value after the shared metatable was mutated.

---

## Test 174 — `Deep __index Chain (20 Levels)`

Builds a 20-level deep `__index` chain, with the value only at the bottom. Reads from the top and checks it resolves correctly. Also tests that the chain doesn't stack-overflow the executor.

**Fails when:** the value can't be found, or the lookup causes a stack overflow.

---

## Test 175 — `__eq Different Type Rejection`

Defines `__eq` on a table. Compares the table with a string using `==`. In Lua, `__eq` only fires when both operands have the same metatable — mixed types should not dispatch it.

**Fails when:** `__eq` fires across incompatible types.

---

## Test 176 — `Metatable Swap During Access`

On first access, the `__index` function swaps the object's metatable to a different one. On the second access, the new metatable's `__index` fires. Verifies the swap takes effect between calls.

**Fails when:** the original metatable is cached and the swap is ignored.

---

## Test 177 — `Self-Referencing __index`

Sets a table's `__index` to itself. Reads a key that exists in the table — it should be found. Reads a missing key — it should resolve to `nil` without infinite recursion.

**Fails when:** missing key lookup infinite-loops, or `__index` chain corrupts results.

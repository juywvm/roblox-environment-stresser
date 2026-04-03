# Error Handling & Control Flow

Tests covering `pcall`, `xpcall`, `error()`, error levels, error objects, multi-return through pcall, and general control flow edge cases.

---

## Test 27 — `Error Object Consistency`

Throws a table as an error: `error({code = 1, msg = "test"})`. Catches it with `pcall`. Checks the table is returned intact with correct fields.

**Fails when:** the error is coerced to a string or loses its fields.

---

## Test 78 — `Pcall Multi-Return Tunneling`

Returns multiple values through `pcall`. Checks all values propagate correctly from the called function through pcall's return list.

**Fails when:** values are dropped or truncated.

---

## Test 89 — `Error Handling Levels`

Calls `error("msg", 2)` inside a nested function. Checks the error appears to originate from the caller's level (level 2), not the thrower's level.

**Fails when:** error levels are ignored or off by one.

---

## Test 97 — `XPcall Handler Environment`

Uses `xpcall` with a handler that reads a variable from its closure. Verifies the handler's environment is intact when called.

**Fails when:** the handler's upvalues are inaccessible from inside `xpcall`.

---

## Test 100 — `Function Argument Dropping (Select)`

Calls `select(3, "a", "b", "c", "d", "e")`. Expects `"c", "d", "e"` as results. Also tests `select("#", ...)` for argument counting.

**Fails when:** `select` returns wrong offset or count.

---

## Test 116 — `XPcall Error Object Preservation`

Throws a table error through `xpcall`. The handler receives it and passes it through. Checks the full table is still intact after the handler.

**Fails when:** `xpcall` converts table errors to strings.

---

## Test 247 — `Nested pcall/xpcall Depth (10 Layers)`

Nests 10 levels of `pcall`. Each level calls the next. The innermost returns `"DEEP"`. Checks it propagates all the way back up.

**Fails when:** nested pcalls overflow the stack or lose the return value.

---

## Test 248 — `Error Object Through pcall Layers`

Throws a table error through 2 pcall layers (pcall inside a pcall inside a pcall). Checks the table is still accessible with correct fields at the outermost layer.

**Fails when:** table error gets stringified at any layer.

---

## Test 267 — `Pcall With Nil Function`

Calls `pcall(nil)`. Expects `false` as the first return (calling nil should error).

**Fails when:** `pcall(nil)` somehow succeeds.

---

## Test 268 — `Xpcall Handler Error Resilience`

The main function errors. The handler also errors. Checks `xpcall` returns `false` without crashing.

**Fails when:** a handler that errors causes the executor to crash or deadlock.

---

## Test 269 — `Error With Table Message`

Passes a table to `error(obj, 0)`. Catches with `pcall`. Checks `type(e) == "table"` and `e.x == 1`.

**Fails when:** the table is coerced to a string.

---

## Test 294 — `Multiple Return Values Propagation`

A function returns `1, "two", true, nil, 5`. All five values are captured and checked individually.

**Fails when:** any value is dropped, especially around the `nil` in the middle.

---

## Test 296 — `Tail Call Stack Conservation`

A function tail-calls itself 10,000 times. Checks the result comes back correctly.

**Fails when:** the recursion blows the stack because tail calls aren't optimized.

---

## Test 334 — `Next On Empty Table`

Calls `next({})`. Expects `nil`.

**Fails when:** `next` on empty table returns non-nil.

---

## Test 335 — `Select Negative Index`

Calls `select(-1, "a", "b", "c")`. If supported, expects `"c"` (last argument).

---

## Test 339 — `Pcall Full Return Passthrough`

`pcall` wraps a function returning `1, 2, 3, 4`. Checks `s == true` and all four values are present.

**Fails when:** pcall drops return values.

---

## Test 340 — `Xpcall Handler Return Value`

Handler returns `"handled: " .. err`. Checks the return from `xpcall` includes `"handled"`.

**Fails when:** handler return value is dropped.

---

## Test 362 — `Pcall Large Return Count`

Returns 8 values through pcall. Checks first and last are correct.

**Fails when:** pcall truncates return values.

---

## Test 363 — `Error Level Parameter`

Calls `error("msg", 2)` from `inner()`. Catches in a pcall. Checks the error message reaches the catcher.

**Fails when:** error message is lost.

---

## Test 370 — `Pcall Method-Style Call`

Calls `pcall(part.IsA, part, "BasePart")`. Checks `result == true`.

**Fails when:** method-style pcall call fails.

---

## Test 405 — `Multiple Assignment Statement`

Assigns `a, b, c = 1, "two", true`. Checks each. Then swaps `a, b = b, a`. Checks the swap.

**Fails when:** multiple assignment is sequential instead of parallel.

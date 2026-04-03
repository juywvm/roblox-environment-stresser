# Debug Library

These tests probe the `debug` library — one of the most powerful tools for inspecting Lua internals. Executors often partially implement or completely break this library.

---

## Test 11 — `Debug Upvalue Access`

Uses `debug.getupvalue` and `debug.setupvalue` on a closure. Gets the upvalue by index, modifies it, and calls the function to confirm the mutation took effect.

**Fails when:** upvalue access returns wrong values, or mutation doesn't change behavior.

---

## Test 12 — `Debug Info & Proto`

Calls `debug.getproto` on a function that has a nested function. Verifies:
- A proto is returned
- It's callable
- Calling it returns the expected value

Also calls `debug.info` and checks it returns a results table.

**Fails when:** `debug.getproto` returns `nil` or an uncallable value.

---

## Test 54 — `Debug Constants Integrity (getconstants)`

Calls `debug.getconstants` on a function containing known string literals. Verifies the known strings appear in the returned constants table.

**Fails when:** the constants table is missing, doesn't include string literals, or is the wrong type.

---

## Test 57 — `Deep Stack Reconstruction`

Builds a deep call stack (10 levels) and calls `debug.traceback()` from the innermost function. Checks the traceback string contains meaningful level info.

**Warns when:** the traceback is missing levels, which means stack info is being stripped.

---

## Test 115 — `Debug.info (Luau) vs Debug.getinfo (Legacy)`

Checks that `debug.info` (the Luau-native version) exists. Also checks for `debug.getinfo` (legacy Lua 5.1). Verifies both return meaningful data when called on a test function.

**Warns when:** only one exists, because some executors expose only one.

---

## Test 188 — `Debug.getstack Level 0`

Inside a function with a known local variable (`x = 12345`), calls `debug.getstack(0, 1)` to get the first stack slot. Expects to get `12345` back.

**Fails when:** `debug.getstack` returns a wrong value or is unavailable.

---

## Test 189 — `Debug.setstack Type Mutation`

Creates a coroutine containing a local number. Suspends it at a yield. Calls `debug.setstack` on the suspended coroutine to change the local from a number to a string. Resumes and checks the local's new type.

**Passes when:** the mutation is reflected, or `debug.setstack` isn't available (skipped).

---

## Test 190 — `Debug.getconstants Literal Discovery`

Creates a function with the unique string `"UNIQUE_LITERAL_XYZ"` and number `98765`. Calls `debug.getconstants` and scans for both values.

**Warns when:** the string literal isn't found (may be inlined or optimized away by the executor's Luau compiler).

---

## Test 191 — `Debug.getproto Nested Extraction`

Defines an `outer` function containing an `inner` function. Calls `debug.getproto(outer, 1, true)` to extract the nested proto. Calls the extracted proto and checks it returns `"INNER"`.

**Fails when:** the proto extraction fails, returns the wrong function, or the call crashes.

---

## Test 192 — `Debug.info on C Functions`

Calls `debug.info(print, "n")` — tries to get debug info about the C function `print`. In Luau, this returns `nil` without crashing.

**Fails when:** calling `debug.info` on a C function throws an error or crashes.

---

## Test 193 — `Debug.traceback Format Verification`

Calls `debug.traceback("MARKER", 1)` and checks:
- The return type is `string`
- The string contains `"MARKER"`

**Fails when:** `debug.traceback` returns a non-string or swallows the custom message.

---

## Test 194 — `Debug.getinfo Upvalue Count`

Defines a closure capturing 3 upvalues. Calls `debug.getinfo(fn)` and reads `.nups`. Expects `3`.

**Fails when:** `nups` is wrong (executor changed the function or lied about upvalue count).

---

## Test 195 — `Debug.getproto Boundary Check`

Calls `debug.getproto` with index `999` on a function with no nested protos. Expects either an error or `nil` — not a crash.

**Fails when:** out-of-bounds access crashes the executor instead of erroring gracefully.

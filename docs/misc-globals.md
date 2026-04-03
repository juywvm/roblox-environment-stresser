# Misc Globals & OS

Tests for standard globals, `os` library, `tick()`, `game` metadata properties, and other utilities.

---

## Test 19 — `Vararg Logic & Nil Handling`

Passes `("a", nil, "c")` as varargs. Inside the function, calls `select("#", ...)` and checks count is `3`. Checks `select(2, ...)` returns `nil` as the first value.

**Fails when:** vararg nil counting is wrong.

---

## Test 24 — `Memory Address Entropy`

Creates two new tables in a loop and compares their `tostring` addresses. Expects them to differ (randomized memory layout). Also checks the format matches `table: 0x...`.

**Warns when:** addresses are sequential or identical (ASLR may be disabled).

---

## Test 26 — `Environment Sandboxing (setfenv)`

Sets a custom environment on a function using `setfenv`, where `math.abs` is replaced with a fake. Calls the function and checks it uses the fake. Verifies the outer `math.abs` is unaffected.

**Fails when:** `setfenv` doesn't sandbox the function correctly.

---

## Test 28 — `Legacy Iterators (foreach)`

Checks `table.foreach` and `table.foreachi` exist and work correctly on both hash and array tables.

**Fails when:** legacy iteration stops early or visits wrong keys.

---

## Test 34 — `Stack Overflow Resilience`

Calls a function recursively until stack overflow. Catches the error with `pcall`. Checks an error is returned (not a crash).

**Fails when:** stack overflow crashes the executor instead of being caught.

---

## Test 49 — `Operator Precedence (Math Order)`

Evaluates `2 + 3 * 4` and checks it's `14` (not `20`). Evaluates `2 ^ 3 ^ 2` and checks it's `512` (right-associative).

**Fails when:** operator precedence is wrong.

---

## Test 51 — `String Format Argument Tolerance`

Calls `string.format("%d %s", 42, "hello")`. Checks result. Also passes fewer arguments than format expects and checks if it errors gracefully.

---

## Test 63 — `Sparse Select & None-Return`

Passes `nil` values to `select` and checks the count. Tests `select("#", nil, nil, nil)` — should return `3`.

**Fails when:** nil args in `select` aren't counted.

---

## Test 68 — `Vararg Propagation Chain`

Passes varargs through 3 function layers `f → g → h`. The inner function counts and returns them. Checks they survive all hops.

**Fails when:** varargs are dropped at any level.

---

## Test 71 — `Number Base Parsing (tonumber)`

Calls `tonumber("FF", 16)` → `255`. `tonumber("77", 8)` → `63`. `tonumber("11", 2)` → `3`.

**Fails when:** base-n parsing returns wrong values.

---

## Test 86 — `os.time Date Reconstruction`

Gets `os.time()`. Passes it to `os.date("*t", t)`. Checks the returned table has year, month, day, hour, min, sec fields with reasonable values.

**Fails when:** `os.date("*t", ...)` is unsupported or returns wrong structure.

---

## Test 87 — `For-Loop Limit Caching`

Creates a table. In a `for i = 1, #t do` loop, appends to the table. Checks the loop doesn't iterate over the newly added elements (limit is cached at loop start).

**Fails when:** the loop limit is re-evaluated each iteration.

---

## Test 99 — `Wait Resolution & Return Count`

Calls `task.wait(0)`. Checks it returns at least one number (elapsed time). Optionally checks it returns exactly 2 values.

**Fails when:** `task.wait` returns nothing or a non-number.

---

## Test 102 — `C-Stack Overflow Check`

Mutually recursive C-boundary calls (Lua → C → Lua → ...) until overflow. Catches with pcall. Verifies the error is catchable.

**Fails when:** C-stack overflow isn't caught.

---

## Test 104 — `Vector3 NaN Poisoning`

Creates a `Vector3` from NaN components. Checks the components are still NaN (not silently fixed). Then does arithmetic with it and checks NaN propagates.

**Fails when:** NaN is silently converted to `0` in Vector3.

---

## Test 112 — `Math Huge (Infinity) Formatting`

Checks `tostring(math.huge)`, comparisons involving `math.huge`, and arithmetic rules.

---

## Test 113 — `Negative Zero (-0) Signedness Fingerprint`

Checks whether `-0 == 0` is `true`. Checks whether `1/(-0)` is `-inf` (signaling that `-0` is negative zero in IEEE 754).

**Fails when:** `-0` collapses to `+0` entirely.

---

## Test 120 — `Vararg Select Gap Detection`

Passes varargs with nil gaps. Uses `select("#", ...)` to count. Checks nil gaps are counted as args.

---

## Test 325 — `Os.clock Monotonic Increase`

Calls `os.clock()` before and after a busy loop. Checks `b >= a`.

**Fails when:** `os.clock` goes backwards.

---

## Test 326 — `Os.time Epoch Sanity`

Checks `os.time()` is a number greater than `1700000000` (after November 2023).

**Fails when:** `os.time` returns a stale or zero value.

---

## Test 327 — `Tick Function Availability`

If `tick()` exists, checks it returns a positive number.

---

## Test 328-330 — `Game Metadata Properties`

- **PlaceId**: must be a number
- **GameId**: must be a number  
- **JobId**: must be a string

---

## Test 331 — `Workspace Camera Instance Check`

Checks `workspace.CurrentCamera` is a Camera Instance with a valid `CFrame` and `ViewportSize`.

---

## Test 342 — `DistributedGameTime Check`

Checks `workspace.DistributedGameTime` is a non-negative number.

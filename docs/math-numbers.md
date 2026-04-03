# Math & Numbers

Tests for numerical correctness, IEEE 754 compliance, edge case handling (NaN, infinity, subnormals, signed zero), and Luau math extensions.

---

## Test 20 — `IEEE 754 Precision (Double)`

Verifies that `0.1 + 0.2` does **not** equal `0.3` (because it shouldn't in IEEE 754 floating point). Then checks the error is within `1e-16`. This is a sanity check that floating point isn't being simplified.

**Fails when:** `0.1 + 0.2 == 0.3` returns `true` (executor is using integer math or decimal rounding).

---

## Test 33 — `math.sign Logic & Zero Handling`

Checks `math.sign(-5) == -1`, `math.sign(0) == 0`, `math.sign(5) == 1`. Also checks `math.sign(-0)` — negative zero should return `0`, not `-1`.

**Fails when:** `math.sign(0)` returns `1` or `-1`, or `math.sign` is missing.

---

## Test 46 — `math.random Invalid Range`

Calls `math.random(10, 5)` (min > max). Expects this to throw an error, not silently return a garbage value.

**Fails when:** the invalid range is accepted without error.

---

## Test 58 — `Floating Point Loop Precision`

Runs a floating point accumulation loop: `sum += 0.1` for 10 iterations. Checks `sum` is within `1e-10` of `1.0`. Tests accumulated rounding error stays within expected bounds.

**Warns when:** sum is further from 1.0 than expected.

---

## Test 66 — `High-Precision Math Constants (PI)`

Checks `math.pi == 3.141592653589793` to full double precision. Then verifies `math.sin(math.pi)` is within `1e-15` of `0`.

**Fails when:** `math.pi` is a truncated approximation.

---

## Test 72 — `Modulo vs fmod Logic`

Checks the sign behavior of `%` vs `math.fmod`:
- `-7 % 3` should be `2` (Lua `%` matches the sign of the divisor)
- `math.fmod(-7, 3)` should be `-1` (matches the sign of the dividend)

**Fails when:** the signs are wrong, indicating a non-standard modulo implementation.

---

## Test 77 — `Floating Point Decomposition`

Calls `math.modf(3.75)` and checks integer part is `3` and fractional part is within `1e-10` of `0.75`. Tests both positive and negative inputs.

**Fails when:** integer or fractional part is wrong.

---

## Test 140 — `Atan2 Signed Zero Phase Check`

Calls `math.atan2(0, -1)` — should return `π` (exactly `math.pi`). Calls `math.atan2(-0, -1)` — should return `-π`. This reveals whether the executor correctly propagates signed zero through atan2.

**Fails when:** both calls return the same value (signed zero isn't honored).

---

## Test 141 — `Frexp/Ldexp Mantissa Reconstruction`

Calls `math.frexp(6.5)` — expects mantissa `0.8125` and exponent `3`. Then calls `math.ldexp(0.8125, 3)` and checks it reconstructs `6.5`.

**Fails when:** `frexp` returns wrong mantissa/exponent, or `ldexp` can't reconstruct the original.

---

## Test 142 — `Modulo vs. Fmod Sign Logic`

More exhaustive version of Test 72, testing multiple negative operand combinations to confirm consistent sign behavior.

**Fails when:** any combination produces wrong sign.

---

## Test 143 — `String Format Scientific Threshold`

Checks the boundary where `string.format("%g", n)` switches from decimal to scientific notation. Standard behavior switches around `1e-4` and `1e14`.

**Warns when:** the switch happens at a different threshold (executor using non-standard formatting).

---

## Test 144 — `64-bit Integer Precision Cutoff`

Numbers above `2^53` lose precision in IEEE 754 double. Checks that `2^53 + 1 == 2^53` evaluates to `true` (they collapse to the same float).

**Fails when:** the executor is using 64-bit integers instead of doubles, making `2^53 + 1 ~= 2^53`.

---

## Test 204 — `IEEE 754 Subnormal Numbers`

Creates a subnormal number: the smallest positive double, `5e-324`. Checks it's positive, non-zero, and smaller than `1e-307` (a normal minimum).

**Fails when:** subnormals are collapsed to zero or treated as an error.

---

## Test 205 — `NaN Boxing & Propagation`

Creates `NaN = 0/0`. Checks:
- `NaN ~= NaN` is `true`
- `NaN == NaN` is `false`
- `type(NaN)` is `"number"`
- `NaN + 1 ~= NaN + 1` is `true` (propagates through arithmetic)
- Table key `NaN` should error or produce undefined behavior

**Fails when:** any NaN comparison returns the wrong result.

---

## Test 206 — `Signed Zero in Table Keys`

Checks whether `0` and `-0` are the same table key. In IEEE 754, `-0 == 0` is `true`, so they should map to the same key.

**Fails when:** `t[-0]` and `t[0]` are stored as different keys.

---

## Test 207 — `Machine Epsilon Boundary`

Finds machine epsilon: the smallest `e` such that `1 + e > 1`. Checks it's close to `2.2e-16` and that `1 + e/2 == 1`.

**Fails when:** epsilon is wildly different from IEEE 754 expected value.

---

## Test 208 — `Infinity Arithmetic Rules`

Checks:
- `1/0 == math.huge` (`inf`)
- `-1/0 == -math.huge` (`-inf`)
- `inf + inf == inf`
- `inf - inf` is NaN
- `inf * 0` is NaN
- `-1/inf == 0`

**Fails when:** any of these standard IEEE 754 results is wrong.

---

## Test 273 — `Math.clamp Boundary Precision`

Calls `math.clamp` with values below, above, and within range. Also tests clamping when `min == max`.

**Fails when:** `math.clamp` is missing or clamping goes to the wrong bound.

---

## Test 274 — `Math.log Custom Base`

Calls `math.log(8, 2)` and checks result is `3.0 ± 1e-10`. Calls `math.log(1000, 10)` and checks result is `3.0 ± 1e-10`.

**Fails when:** `math.log` doesn't accept a second argument (base), or returns wrong values.

---

## Test 275 — `Math.round Half-Up Behavior`

Checks `math.round(2.5) == 3`, `math.round(3.5) == 4`, and `math.round(-1.5)` returns either `-2` or `-1` (both are valid half-rounding behaviors).

**Fails when:** `math.round` is missing or rounds in the wrong direction.

---

## Test 301 — `Math.max/min Many Arguments`

Calls `math.max(1, 5, 3, 9, 2, 7)` and checks `== 9`. Calls `math.min(1, 5, 3, 9, 2, 7)` and checks `== 1`.

**Fails when:** multi-argument math.max/min returns wrong result.

---

## Test 302 — `Math.modf Decomposition`

Calls `math.modf(3.75)` and checks integer part = `3`, fractional part ≈ `0.75`. Tests `-3.75` too.

**Fails when:** decomposition is wrong or fractional part has wrong sign.

---

## Test 337 — `Math.abs Edge Values`

Tests `math.abs(-0) == 0`, `math.abs(math.huge) == math.huge`, `math.abs(-math.huge) == math.huge`, `math.abs(NaN) ~= math.abs(NaN)`.

**Fails when:** any of these return unexpected values.

---

## Test 360 — `Math.sign Return Values`

Checks `math.sign(5) == 1`, `math.sign(-5) == -1`, `math.sign(0) == 0`.

**Fails when:** `math.sign` is missing or returns wrong sign.

---

## Test 361 — `Double Precision Close Comparison`

Checks `0.1 + 0.2 ~= 0.3` is true (floating point inequality), but the difference is less than `1e-15`.

**Fails when:** `0.1 + 0.2 == 0.3` (executor using decimal math).

---

## Test 366 — `Math.random Range Bounds`

Calls `math.random(1, 2)` 1000 times. Verifies:
- All values are within `[1, 2]`
- Both `1` and `2` appear at least once

**Fails when:** the range is exclusive rather than inclusive.

---

## Test 398 — `Math.randomseed No Error`

Calls `math.randomseed(42)` and checks it doesn't error.

**Fails when:** `math.randomseed` is missing or errors on a valid seed.

---

## Test 404 — `Math.sin/cos Pythagorean Identity`

For a set of angles, checks `sin(a)^2 + cos(a)^2 == 1` within `1e-10`.

**Fails when:** sin or cos precision is broken.

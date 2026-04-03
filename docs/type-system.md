# Type System

Tests for `type()`, `typeof()`, type coercion, `tostring()`, `tonumber()`, and type-related behaviors.

---

## Test 61 — `Strict Type Coercion (Boolean/Nil)`

Verifies that `nil` and `false` are both falsy in conditionals. Verifies that `0` and `""` are truthy (unlike in some other languages).

**Fails when:** `0` or `""` is treated as falsy.

---

## Test 265 — `Typeof vs Type Distinction`

Checks that `type(Vector3.new())` returns `"userdata"` but `typeof(Vector3.new())` returns `"Vector3"`. Same for `game`: `type` returns `"userdata"`, `typeof` returns `"Instance"`.

**Fails when:** `type` and `typeof` return the same value for Roblox userdata.

---

## Test 266 — `Type Function Primitive Coverage`

Calls `type()` on all Lua primitives:
- `nil` → `"nil"`
- `true` → `"boolean"`
- `42` → `"number"`
- `"s"` → `"string"`
- `{}` → `"table"`
- `print` → `"function"`
- coroutine thread → `"thread"`

**Fails when:** any primitive returns the wrong type string.

---

## Test 271 — `Tostring Universal Coverage`

Calls `tostring()` on `nil`, `true`, `42`, `{}`, and `print`. Checks each returns a string.

**Fails when:** `tostring` on any standard type returns non-string or errors.

---

## Test 272 — `Tonumber Edge Cases`

Tests:
- `tonumber("  42  ")` → `42` (whitespace stripped)
- `tonumber("0xFF")` → `255` or `nil` (implementation-defined)
- `tonumber("")` → `nil`
- `tonumber("  ")` → `nil`
- `tonumber("1e3")` → `1000`

**Fails when:** tonumber accepts or rejects unexpected inputs.

---

## Test 299 — `Infinity Tostring Format`

Checks `tostring(math.huge) == "inf"` and `tostring(-math.huge) == "-inf"`.

**Fails when:** the executor uses a different format like `"inf"` vs `"1/0"` vs `"Inf"`.

---

## Test 300 — `NaN Tostring Format`

Checks `tostring(0/0):lower():find("nan")`.

**Fails when:** NaN is formatted as something unexpected.

---

## Test 344 — `Rawlen String and Table`

Checks `rawlen("Hello") == 5` and `rawlen({1,2,3}) == 3`.

**Fails when:** `rawlen` on strings or tables returns wrong values.

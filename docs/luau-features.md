# Luau-Specific Features

Tests for features that are unique to Luau (Roblox's Lua fork) and don't exist in standard Lua 5.1 or 5.4.

---

## Test 31 — `Luau Generalized Iteration (No-Pairs)`

Uses the new `for k, v in t do` syntax without calling `pairs`. In Luau, this is valid and equivalent to `for k, v in pairs(t) do`.

**Fails when:** the executor's parser doesn't support generalized iteration.

---

## Test 50 — `Luau Random Object (State & Cloning)`

Creates a `Random.new(seed)` object. Gets values from it. Then calls `clone()` on the Random object and checks the clone produces the same sequence from that point.

**Fails when:** `Random` is missing, or `clone` doesn't produce identical subsequent values.

---

## Test 136 — `Roblox Perlin Noise Determinism`

Calls `math.noise(x, y, z)` — Roblox's Perlin noise function. With the same inputs, it should always return the same value.

**Fails when:** `math.noise` returns different values for the same inputs, or is missing.

---

## Test 155 — `SharedTable (Parallel Luau) Integrity`

If `SharedTable` is available, creates one, sets a key, reads it back. SharedTables are designed for cross-Actor data sharing.

---

## Test 276 — `Luau String Interpolation`

If the executor supports backtick strings, tests `` `Value is {val}` `` where `val = 42`. Expects `"Value is 42"`. This is wrapped in `loadstring` to avoid parse failure on unsupported executors.

**Passes when:** the interpolation is either supported and correct, or not supported at all (loadstring returns nil → skip).

---

## Test 277 — `Luau If-Expression`

Tests `local x = if true then "YES" else "NO"`. Wrapped in `loadstring`. Checks result if supported.

**Passes when:** the if-expression is either supported and returns `"YES"`, or not supported (loadstring returns nil → skip).

---

## Test 70 — `RNG Determinism (math.randomseed)`

Seeds with a fixed value, generates 5 numbers. Seeds again with same value, generates 5 more. Checks both sequences are identical.

**Fails when:** seeding doesn't produce deterministic output.

---

## Test 151 — `New Audio API (Wire Connection Logic)`

If `AudioDeviceInput` and `Wire` are available (Roblox's modern audio system), creates and connects them. Checks the Wire's `SourceInstance` is set correctly.

---

## Test 152 — `UIDragDetector Properties & Enums`

If `UIDragDetector` exists, checks its `ResponseStyle` property is an `EnumItem` from `Enum.UIDragDetectorResponseStyle`.

---

## Test 153 — `Workspace Blockcast (Physics Query)`

Calls `workspace:Blockcast(cframe, size, direction)`. Checks return is either `nil` or a valid raycast result type.

---

## Test 157 — `CatalogSearchParams (Complex Struct)`

If `CatalogSearchParams` is available, creates one, sets `SearchKeyword`, reads it back.

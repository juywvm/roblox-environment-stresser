# Executor & Environment APIs

Tests in this category verify that executor-specific global functions exist, behave correctly, and are not silently faked. These are the first things that reveal whether an environment is genuine or emulated.

---

## Test 1 — `HttpService:GenerateGUID Integrity`

Calls `HttpService:GenerateGUID(false)` and validates that the returned value:
- Is a `string`
- Has exactly 36 characters (the standard UUID format)
- Contains hyphens at positions 9, 14, 19, and 24
- Is unique across repeated calls

**Fails when:** the executor returns a fake GUID with wrong length, or the same GUID on every call.

---

## Test 5 — `Crypt Library: Integrity & Logic`

Checks that the `crypt` global table exists and that `crypt.base64encode` / `crypt.base64decode` form a correct round-trip. Encodes a known string and decodes it back, comparing the result.

**Fails when:** the base64 decode doesn't match the original, or the functions don't exist.

---

## Test 6 — `Filesystem API Signatures`

Checks that `writefile`, `readfile`, `isfile`, `isfolder`, `makefolder`, `listfiles`, and `delfile` exist. Writes a test file, reads it back, and confirms the content matches. Then deletes it.

**Fails when:** any filesystem function is missing, or write/read produces a mismatch.

---

## Test 7 — `HUI Protection (gethui)`

Calls `gethui()` and verifies the returned value is a `ScreenGui` Instance. The hidden UI container must exist and be a valid Instance. Executors sometimes return a plain table or `nil` here.

**Fails when:** `gethui` is missing or returns a non-Instance value.

---

## Test 8 — `Drawing Library Structure`

Checks that `Drawing` exists as a table and that `Drawing.new("Line")` returns an object with expected properties like `Visible`, `Color`, `Thickness`, `From`, and `To`. Calls `Remove()` at the end.

**Fails when:** Drawing is missing, or the returned object doesn't have the expected fields.

---

## Test 9 — `Closure Identity (newcclosure)`

Calls `newcclosure` on a Lua function and verifies:
- The returned closure is a function
- `iscclosure(result)` returns `true`
- `islclosure(result)` returns `false`
- Calling the wrapper returns the same value as the original

**Fails when:** `newcclosure` produces something that doesn't pass `iscclosure`, or the return value changes.

---

## Test 10 — `Hookfunction Logic`

Uses `hookfunction` to replace a local function, saves the original. Verifies:
- The hook is active (calling the function uses the hook)
- The saved original still works correctly

**Fails when:** the hook doesn't activate, or calling the original after hooking crashes.

---

## Test 13 — `Global Environment (getgenv)`

Calls `getgenv()` and checks it returns a table. Then sets a unique key on it and reads it back from the same table reference to confirm it's a real mutable environment object, not a copy.

**Fails when:** `getgenv()` returns a frozen or copied table where mutations don't persist.

---

## Test 14 — `Metatable Manipulation (setreadonly)`

Creates a table, freezes it with `setreadonly(t, true)`, then attempts a write. Expects the write to fail. Then unfreezes with `setreadonly(t, false)` and writes again, expecting success.

**Fails when:** writes succeed on a read-only table, or the unfreeze doesn't work.

---

## Test 15 — `CloneRef Behavior`

Calls `cloneref(game)` and verifies the clone is an Instance of type `DataModel`. Checks that the clone's `ClassName` is correct and it behaves like the real `game` object.

**Fails when:** `cloneref` returns a non-Instance, or the clone has wrong metadata.

---

## Test 16 — `WebSocket Library Structure`

Checks that `WebSocket` (or `syn.websocket`) has a `connect` function. Does not actually open a connection — just validates the API surface.

**Fails when:** the WebSocket table is missing or `connect` is not callable.

---

## Test 17 — `Compression Algorithms`

Checks that `lz4compress` and `lz4decompress` exist, compresses a test string, decompresses it, and verifies the result matches the original.

**Fails when:** compression functions are missing or the round-trip corrupts data.

---

## Test 18 — `Actor/Parallel Capability`

Checks whether `Actor` instances and parallel Lua APIs are accessible. This is mostly a capability probe — the test passes even if the feature isn't present, but logs a warning.

---

## Test 43 — `setfenv on C-Functions`

Attempts to call `setfenv` on a C function (like `print`). In standard Lua, this errors — C functions don't have an environment. Verifies the executor respects this behavior.

**Fails when:** `setfenv` succeeds on a C function without error.

---

## Test 55 — `Registry Access (debug.getregistry)`

Calls `debug.getregistry()` and checks the return is a table. The registry is an internal Lua table — it should work in executor contexts but may be guarded in stripped environments.

**Fails when:** `debug.getregistry` is missing or returns a non-table.

---

## Test 56 — `Privileged Metatable Set`

Tests whether `setmetatable` can be called on a userdata object (a Roblox Instance) when a `__metatable` guard exists. Expects this to error. The executor should respect the guard.

**Fails when:** `setmetatable` on a locked Instance succeeds without error.

---

## Test 93 — `System Integrity Check`

Checks that `getrenv`, `getgenv`, and `getfenv` are all present and return distinct tables — the game env, executor env, and function env should not overlap.

**Fails when:** any of the three are missing or two of them return the same table.

---

## Test 117 — `Setfenv on C-Function Protection`

A more aggressive version of test 43 — tries setting a completely different table as the environment of `math.sin` and then calls it. Expects the call to fail or raise an error.

**Fails when:** calling a C function with a replaced environment silently succeeds.

---

## Test 211 — `Hookfunction Double-Hook Chain`

Hooks a function twice. Verifies that:
1. The active version is the second hook
2. The first saved original is the first hook
3. The first hook's saved original is the real function

**Fails when:** the hook chain breaks — either the second hook doesn't activate, or the originals return wrong values.

---

## Test 212 — `Newcclosure Nested Wrapping`

Wraps a function with `newcclosure`, then wraps the result again. Checks that:
- The double-wrapped version still passes `iscclosure`
- Calling it returns the correct value from the original function

**Fails when:** double-wrapping produces a broken closure or wrong return value.

---

## Test 213 — `Cloneref Identity Separation`

Calls `cloneref(game)`. Checks that `typeof(clone) == "Instance"` and `clone.ClassName == "DataModel"`. Additionally warns if `rawequal(clone, game)` — they should be distinct references.

**Fails when:** `cloneref` returns a non-Instance or throws an error.

---

## Test 214 — `Getgenv vs Getrenv Isolation`

Sets a unique random key on `getgenv()`. Then checks that the same key is **not** present in `getrenv()`. The executor's environment and the game's environment must be isolated.

**Warns when:** the key leaks from the executor environment into the game environment (isolation failure).

---

## Test 215 — `Filesystem Binary Round-Trip`

Writes a 256-byte string (every byte from 0x00 to 0xFF) to a temp file, reads it back, and checks byte-for-byte equality. Also checks `isfile` and `delfile` work correctly.

**Fails when:** any byte is mutated during write/read, or file operations don't work.

---

## Test 216 — `Filesystem Null Byte Preservation`

Writes a string containing embedded `\0` null bytes to a file and reads it back. Checks that the length is still correct (null bytes must not truncate the string).

**Fails when:** the file system truncates the string at the first null byte.

---

## Test 253 — `Crypt Base64 Binary Integrity`

Encodes all 256 byte values with `crypt.base64encode`, then decodes with `crypt.base64decode`. Verifies the round-trip is lossless.

**Fails when:** any byte is corrupted during encode/decode.

---

## Test 254 — `Crypt Hash Known Value (SHA256)`

Hashes an empty string with `crypt.hash("", "sha256")` and compares against the known SHA256 hash of an empty string: `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855`.

**Warns when:** the hash doesn't match (indicates a wrong algorithm or bad implementation).

---

## Test 333 — `Getfenv Table Return`

Calls `getfenv(0)` and verifies it returns a table. Level 0 is the global environment in Lua 5.1-style APIs.

**Fails when:** `getfenv` returns a non-table or errors.

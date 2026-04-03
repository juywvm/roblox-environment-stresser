# Buffer Library

Tests for Luau's native `buffer` library — a typed binary buffer that enables direct memory layout control.

---

## Test 150 — `Luau Buffer Library Integrity`

Checks that the `buffer` global exists. Creates a buffer with `buffer.create(8)`. Writes and reads a `u8` value. Verifies the value survived the write/read.

**Fails when:** `buffer` is nil, or write/read produces the wrong value.

---

## Test 259 — `Buffer Float32 Precision`

Writes `3.14` as a 32-bit float to a buffer. Reads it back. Checks the value is within `0.001` of `3.14`.

**Fails when:** `writef32`/`readf32` is imprecise beyond floating point tolerance.

---

## Test 260 — `Buffer Float64 Precision`

Writes `math.pi` as a 64-bit double. Reads it back. Checks exact equality — doubles should be lossless.

**Fails when:** `writef64`/`readf64` loses any precision.

---

## Test 261 — `Buffer Out-of-Bounds Access`

Creates a 4-byte buffer. Tries to read at offset `10`. Expects an error. Tries to write at offset `10`. Expects an error.

**Fails when:** out-of-bounds access succeeds silently (memory safety violation).

---

## Test 262 — `Buffer Length Check`

Creates `buffer.create(42)`. Calls `buffer.len(b)`. Checks `== 42`.

**Fails when:** `buffer.len` returns wrong size.

---

## Test 263 — `Buffer String Round-Trip`

Creates a buffer from `"Hello\0World"` with `buffer.fromstring`. Converts back with `buffer.tostring`. Checks exact equality including the null byte.

**Fails when:** null bytes are truncated during string conversion.

---

## Test 264 — `Buffer Copy Operation`

Creates two 4-byte buffers. Writes `0xAA` and `0xBB` to bytes 0 and 1 of `src`. Copies 2 bytes from `src` to `dst`. Reads from `dst` and checks both bytes.

**Fails when:** `buffer.copy` copies wrong bytes or wrong count.

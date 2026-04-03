# Bit Operations & Crypt

Tests for `bit32`, bitwise operations, and the `crypt` library (base64, hashing).

---

## Test 81 — `Bitwise Extraction`

Uses `bit32.extract` to extract bits from a number at a given position and width. Checks the result matches the manually computed expected value.

**Fails when:** `bit32.extract` is missing or returns wrong bits.

---

## Test 91 — `Bitwise Operations`

Tests `bit32.band`, `bit32.bor`, `bit32.bxor`, `bit32.bnot`. Checks all against known expected values.

**Fails when:** any bitwise operation produces wrong output.

---

## Test 255 — `Bit32 Shift by 32`

Checks `bit32.lshift(1, 32) == 0` and `bit32.rshift(0xFFFFFFFF, 32) == 0`.

**Fails when:** shifting by 32 produces non-zero (wrong wraparound behavior).

---

## Test 256 — `Bit32 btest Operation`

Checks `bit32.btest(0xFF, 0x0F) == true` (overlapping bits). Checks `bit32.btest(0xF0, 0x0F) == false` (no overlap).

**Fails when:** `btest` returns wrong boolean.

---

## Test 257 — `Bit32 Replace Field`

Calls `bit32.replace(0, 0xF, 4, 4)` — replaces bits 4-7 with `0xF`. Expects `0xF0`.

**Fails when:** `bit32.replace` doesn't place bits at the correct position.

---

## Test 253 — `Crypt Base64 Binary Integrity`

Encodes all 256 byte values and decodes them. Checks full round-trip. *(See also Executor APIs)*

---

## Test 254 — `Crypt Hash Known Value (SHA256)`

Hashes `""` with SHA256, compares to the known hash. *(See also Executor APIs)*

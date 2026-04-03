# Strings & Patterns

Tests covering string operations, pattern matching, binary string handling, `string.pack`/`unpack`, format edge cases, and string library quirks.

---

## Test 25 — `Binary String Integrity (Null Byte)`

Creates a string with embedded null bytes (`"\0"`) and verifies `#str` returns the correct length (null bytes must not terminate the string).

**Fails when:** the string is truncated at the first `\0`.

---

## Test 44 — `string.gsub Table Lookup Logic`

Passes a table as the replacement argument to `string.gsub`. Each captured match is used as the key into the table. Verifies replacements are made using the table values.

**Fails when:** table lookups in gsub don't work, or captures are wrong.

---

## Test 53 — `String gmatch Empty Pattern`

Calls `string.gmatch("abc", "")` — an empty pattern matches every position including before each character. Counts the iterations and checks the count is `#str + 1`.

**Fails when:** empty pattern matching returns wrong iteration count.

---

## Test 60 — `Callback Error Tunneling (gsub)`

Passes a Lua function that errors to `string.gsub`. The error should propagate out of `gsub` to the `pcall` wrapping the call.

**Fails when:** `gsub` swallows errors from replacement callbacks.

---

## Test 69 — `String Repetition Logic`

Calls `string.rep("ab", 3)` — expects `"ababab"`. Also verifies the separator form `string.rep("ab", 3, ",")` returns `"ab,ab,ab"`.

**Fails when:** the separator form isn't supported, or the result is wrong.

---

## Test 74 — `String Byte Slicing`

Calls `string.byte("Hello", 2, 4)` and checks the three returned values are `101`, `108`, `108` (e, l, l).

**Fails when:** range return values from `string.byte` are wrong or truncated.

---

## Test 88 — `Balanced String Matching (%b)`

Uses the `%b()` pattern to match balanced parentheses. Tests `string.match("(hello)", "%b()")` returns `"(hello)"`.

**Fails when:** `%b` patterns aren't supported or produce wrong results.

---

## Test 106 — `String Formatting Buffer Overflow Simulation`

Calls `string.format("%s", string.rep("X", 100000))`. Verifies the result has the expected length. Some formatters truncate large strings.

**Fails when:** the output is shorter than `100000` characters.

---

## Test 109 — `Binary String Null-Byte Poisoning`

Creates a string with null bytes in specific positions, searches with `string.find`, and checks the correct positions are found. Null bytes should not affect pattern positions.

**Fails when:** `string.find` returns wrong offsets due to null byte mishandling.

---

## Test 114 — `String Pattern Frontier (%f) Check`

Uses `%f[%a]` (frontier pattern) to match word boundaries. Tests on `"hello world"` and checks matches start at `1` and `7`.

**Fails when:** frontier patterns aren't supported or return wrong positions.

---

## Test 118 — `String Pack/Unpack Binary Integrity`

Packs two integers and a float using `string.pack`, then unpacks them. Checks exact values are recovered. Tests both big-endian and little-endian format specifiers.

**Fails when:** pack/unpack loses precision or byte order is wrong.

---

## Test 196 — `String Pattern with Embedded Nulls`

Tests that `string.find` can locate patterns in strings containing `\0`. The null byte must not terminate pattern search.

**Fails when:** the search stops at the first null byte.

---

## Test 197 — `String.char Boundary Values`

Calls `string.char(0)` and `string.char(255)` — checks they return single-character strings. Also verifies `string.char(256)` errors or is clamped.

**Fails when:** `string.char(0)` errors, or `string.char(256)` silently wraps without error.

---

## Test 198 — `String.rep with Separator (Luau)`

Calls `string.rep("X", 4, "-")` and expects `"X-X-X-X"`. This is a Luau/Lua 5.2+ extension.

**Fails when:** the separator argument is ignored.

---

## Test 199 — `String Massive Concatenation Integrity`

Concatenates a short string 10,000 times using a loop. Checks the final length equals `10,000 * len(str)`.

**Fails when:** the concatenation is truncated or produces wrong length.

---

## Test 200 — `String.format %q Control Character Escape`

Calls `string.format("%q", "Hello\nWorld\t!")` and checks the result contains `\n` and `\t` as escaped sequences.

**Fails when:** `%q` doesn't escape control characters.

---

## Test 201 — `String.find Plain Flag Literal Match`

Calls `string.find("2+2=4", "+", 1, true)` — the `true` flag makes it a plain search. Should find `+` at position 2.

**Fails when:** the plain flag is ignored and `+` is treated as a regex quantifier.

---

## Test 202 — `String.pack Multi-Format`

Packs `"hello"`, `42`, and `3.14` into a binary string using multiple format specs. Unpacks and checks all three values.

**Fails when:** any value is corrupted during pack/unpack.

---

## Test 203 — `String.pack Endianness Check`

Packs value `1` as a big-endian 4-byte integer (`>I4`) and as a little-endian 4-byte integer (`<I4`). Checks the raw bytes differ in the expected positions.

**Fails when:** endianness format specifiers are ignored.

---

## Test 270 — `String Interning Equality`

Creates `"Hello" .. "World"` and compares it to the literal `"HelloWorld"`. Checks they're `==` and have the same `#`.

**Fails when:** string concatenation results are not equal to equivalent literals.

---

## Test 297 — `String Lexicographic Comparison`

Checks `"abc" < "abd"`, `"abc" < "abcd"`, `"ABC" < "abc"` (uppercase < lowercase in ASCII). Checks that `"abc" < "abc"` is `false`.

**Fails when:** string comparison doesn't follow byte values.

---

## Test 298 — `String Byte-Level Length`

Creates a UTF-8 encoded `é` (two bytes: `0xC3 0xA9`). Checks `#s == 2` — Lua strings are byte arrays, not character arrays.

**Fails when:** the length reflects character count rather than byte count.

---

## Test 304 — `String.gsub Function Replacement Captures`

Replaces all numbers in `"2+3=5"` by doubling each. Uses a callback: `function(n) return n*2 end`. Expects `"4+6=10"`.

**Fails when:** captures don't reach the callback, or the callback result isn't used.

---

## Test 305 — `String.match Multiple Captures`

Matches `"2025-12-31"` against `"(%d+)-(%d+)-(%d+)"`. Checks all three captures: `"2025"`, `"12"`, `"31"`.

**Fails when:** captures are missing or in wrong order.

---

## Test 306 — `String.split Delimiter Logic`

Splits `"a,b,,c"` by `","`. Expects 4 parts, with the third being an empty string.

**Fails when:** empty segments are collapsed, or `string.split` is missing.

---

## Test 336 — `String.reverse Integrity`

Checks `string.reverse("Hello") == "olleH"`, `string.reverse("") == ""`, `string.reverse("A") == "A"`.

**Fails when:** reverse produces wrong results.

---

## Test 343 — `String Case Conversion`

Checks `string.lower("HELLO") == "hello"`, `string.upper("hello") == "HELLO"`, and `string.lower("123!@#") == "123!@#"`.

**Fails when:** case conversion doesn't work or affects non-alpha characters.

---

## Test 367 — `String.byte Range Return`

Calls `string.byte("ABC", 1, 3)` and checks `a, b, c == 65, 66, 67`.

**Fails when:** range returns are truncated or wrong.

---

## Test 368 — `String.char Multi Argument`

Calls `string.char(72, 101, 108, 108, 111)` and checks the result is `"Hello"`.

**Fails when:** multi-argument `string.char` is unsupported or wrong.

---

## Test 391 — `All 256 Byte Values String`

Builds a string of all byte values from 0 to 255. Checks length is 256. Checks each byte position matches its expected value.

**Fails when:** any byte is altered during string construction.

---

## Test 403 — `String.sub Edge Cases`

Tests `string.sub(s, 1, 1)`, `string.sub(s, -1)`, `string.sub(s, 2, 4)`, and `string.sub(s, 10)` on `"Hello"`.

**Fails when:** negative indices or out-of-range indices are handled incorrectly.

---

## Test 406 — `Number-String Concatenation Coercion`

Checks `"Value: " .. 42 == "Value: 42"`. Also checks `1 .. 2 == "12"`.

**Fails when:** number-to-string coercion in `..` fails or errors.

---

## Test 407 — `String.gmatch All Matches`

Matches all words in `"one two three four"` with `%S+`. Checks count is 4 and first/last words are correct.

**Fails when:** `gmatch` misses matches or returns extra values.

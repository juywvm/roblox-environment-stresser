# Drawing Library

Tests for executor drawing APIs (`Drawing.new`), which let scripts render 2D overlays on screen.

---

## Test 8 — `Drawing Library Structure`

Checks the `Drawing` global exists and `Drawing.new("Line")` returns an object with expected properties. *(See Executor APIs)*

---

## Test 249 — `Drawing.new Line Properties`

Creates a `Drawing.new("Line")`. Sets `From`, `To`, `Color`, `Thickness`, and `Visible`. Reads back `Visible` and `Thickness` to verify they stuck.

**Fails when:** Drawing properties don't persist or `Drawing.new` is missing.

---

## Test 250 — `Drawing.new Circle Properties`

Creates a `Drawing.new("Circle")`. Sets `Position`, `Radius`, `Filled`, and `Visible`. Reads back `Radius` and `Filled`.

**Fails when:** Circle properties are wrong or Drawing is unavailable.

---

## Test 251 — `Drawing.new Text Properties`

Creates a `Drawing.new("Text")`. Sets `Text`, `Position`, `Size`, and `Visible`. Reads back `Text` and `Size`.

**Fails when:** Text properties are wrong.

---

## Test 252 — `Drawing Object Post-Remove Access`

Creates a Line, shows it, then calls `:Remove()`. Tries to read `.Visible` after removal. Depending on the executor, this may error or return `nil`. The test passes either way — it just checks the executor doesn't crash hard.

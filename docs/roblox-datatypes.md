# Roblox Datatypes

Tests for Roblox's built-in value types: `Vector2`, `Vector3`, `CFrame`, `Color3`, `UDim`, `UDim2`, `Region3`, `NumberRange`, `NumberSequence`, `ColorSequence`, `Rect`, and `Ray`.

---

## Test 137 — `CFrame Vector Object Space Transformation`

Creates a CFrame at an offset. Transforms a `Vector3` through it and its inverse. Checks the round-trip restores the original vector within tolerance.

**Fails when:** CFrame multiplication or inverse has precision errors beyond tolerance.

---

## Test 138 — `Ray.Unit Direction Normalization`

Creates a Ray with a known direction. Checks `ray.Direction.Unit` has magnitude `~= 1` within `1e-10`.

**Fails when:** `Unit` returns an unnormalized vector.

---

## Test 139 — `UDim2 Math Metamethod Stability`

Adds two `UDim2` values. Checks the result's X and Y scales and offsets are correct.

**Fails when:** UDim2 addition returns wrong values.

---

## Test 228 — `Color3 Value Clamping`

Creates `Color3.new(2, -1, 0.5)` (out-of-range values). Checks `typeof` is `"Color3"`. Checks `ToHex()` returns a string.

**Fails when:** Color3 crashes on out-of-range values.

---

## Test 229 — `Color3 HSV Round-Trip`

Creates a Color3 from RGB, converts to HSV with `Color3.toHSV`, reconstructs with `Color3.fromHSV`. Checks R, G, B components are within `0.01` of original.

**Fails when:** HSV conversion is imprecise.

---

## Test 230 — `Vector3 Cross Product Right-Hand Rule`

Checks `Vector3.new(1,0,0):Cross(Vector3.new(0,1,0))` equals `Vector3.new(0,0,1)` within `1e-5`.

**Fails when:** cross product returns wrong direction.

---

## Test 231 — `Vector3 Dot Product Orthogonality`

Checks `Vector3.new(1,0,0):Dot(Vector3.new(0,1,0))` is within `1e-10` of `0`.

**Fails when:** dot product of orthogonal vectors is nonzero.

---

## Test 232 — `Vector3.Unit on Zero Vector`

Gets `Vector3.new(0,0,0).Unit`. Expects either NaN components or zero (no crash).

**Fails when:** the executor crashes or throws an unhandled error.

---

## Test 233 — `CFrame Identity Matrix`

Checks `CFrame.new().Position == Vector3.new(0,0,0)`. Multiplies identity by a vector and checks the vector is unchanged.

**Fails when:** identity CFrame transforms points incorrectly.

---

## Test 234 — `CFrame Inverse Cancellation`

Multiplies `cf:Inverse() * cf`. Checks the result's X, Y, Z are all within `0.001` of `0` (equivalent to identity).

**Fails when:** inverse multiplication doesn't cancel out.

---

## Test 235 — `CFrame Euler Angles Round-Trip`

Creates a CFrame from Euler angles `(0.5, 1.0, 0.3)`. Decomposes with `ToEulerAnglesXYZ`. Reconstructs. Checks the reconstructed CFrame is equivalent within `0.001`.

**Fails when:** Euler decomposition/reconstruction loses accuracy.

---

## Test 236 — `Region3 Construction Integrity`

Creates `Region3.new(Vector3.new(-5,-5,-5), Vector3.new(5,5,5))`. Checks `typeof` is `"Region3"` and `.Size` is within `0.01` of `(10, 10, 10)`.

**Fails when:** Region3 size or type is wrong.

---

## Test 237 — `NumberRange Min-Max Validation`

Tries `NumberRange.new(10, 5)` — expects an error (min > max). Creates `NumberRange.new(1, 10)` and checks `.Min == 1`, `.Max == 10`.

**Fails when:** invalid ranges are silently accepted.

---

## Test 312 — `NumberSequence Construction`

Creates `NumberSequence.new(0, 1)`. Checks `typeof` is `"NumberSequence"` and `.Keypoints` is a table with at least 2 entries.

---

## Test 313 — `ColorSequence Construction`

Creates `ColorSequence.new(Color3.new(0,0,0), Color3.new(1,1,1))`. Checks `typeof` is `"ColorSequence"`.

---

## Test 314 — `NumberSequenceKeypoint Creation`

Creates `NumberSequenceKeypoint.new(0.5, 1.0, 0.1)`. Checks `typeof`, `.Time`, `.Value`.

---

## Test 315 — `Rect Datatype Integrity`

Creates `Rect.new(10, 20, 100, 200)`. Checks `Min.X == 10`, `Max.Y == 200`, `Width == 90`, `Height == 180`.

**Fails when:** Rect dimensions are computed incorrectly.

---

## Test 316 — `UDim Arithmetic`

Adds `UDim.new(0.5, 10) + UDim.new(0.3, 5)`. Checks result `Scale ≈ 0.8`, `Offset == 15`.

**Fails when:** UDim addition is wrong.

---

## Test 317 — `Vector2 Magnitude and Unit`

Creates `Vector2.new(3, 4)`. Checks `.Magnitude ≈ 5`. Checks `.Unit.X ≈ 0.6` and `.Unit.Y ≈ 0.8`.

**Fails when:** magnitude or unit is wrong.

---

## Test 318 — `Vector2 Lerp Interpolation`

Lerps from `(0,0)` to `(10,20)` at `t=0.5`. Checks result is `(5, 10)`.

**Fails when:** Lerp result is wrong.

---

## Test 319 — `Vector3 Lerp Accuracy`

Lerps from `(0,0,0)` to `(10,20,30)` at `t=0.25`. Checks result is `(2.5, 5, 7.5)`.

**Fails when:** Lerp result is wrong.

---

## Test 320 — `CFrame.lookAt Direction`

Creates `CFrame.lookAt(origin, target)` pointing along `(0,0,-10)`. Checks `.LookVector.Z ≈ -1`.

**Fails when:** LookVector doesn't point in the expected direction.

---

## Test 347 — `Camera ViewportSize Positive`

Checks `workspace.CurrentCamera.ViewportSize` is a `Vector2` with `X > 0` and `Y > 0`.

---

## Test 348 — `Camera FieldOfView Range`

Checks FOV is a number in range `(0, 180)`.

---

## Test 351 — `Part CFrame Assignment Round-Trip`

Sets `part.CFrame = CFrame.new(10, 20, 30)`. Reads it back and checks components are within `0.01`.

---

## Test 352 — `Part Size Assignment`

Sets `part.Size = Vector3.new(4, 1, 2)`. Reads it back and checks all components within `0.01`.

---

## Test 378 — `CFrame Component Types`

Calls `CFrame.new():GetComponents()`. Checks the result has exactly 12 elements, all numbers.

**Fails when:** component count is wrong or any value is non-numeric.

---

## Test 379 — `Vector3 Max Min Methods`

Checks `Vector3.new(1,5,3):Max(Vector3.new(4,2,6))` returns `(4,5,6)`. Checks `:Min` returns `(1,2,3)`.

---

## Test 380 — `Vector3 FuzzyEq Check`

If `FuzzyEq` exists, checks two nearly-equal vectors are considered equal within a tolerance of `1e-5`.

---

## Test 397 — `Color3.fromRGB Clamping`

Creates `Color3.fromRGB(255, 0, 128)`. Checks `R ≈ 1.0`, `G ≈ 0.0`, `B ≈ 128/255`.

---

## Test 401 — `Vector3 Full Arithmetic`

Tests `+`, `-`, and scalar `*` on Vector3. Checks each result component-by-component.

---

## Test 402 — `Vector2 Full Arithmetic`

Tests `+` and unary `-` on Vector2.

# Roblox Instances & Services

Tests that verify Roblox's core Instance API, service accessibility, hierarchy methods, signals, attribute storage, and general datamodel integrity.

---

## Test 2 — `Workspace & Camera Types`

Verifies `workspace` exists and `typeof(workspace) == "Instance"`. Checks `workspace.CurrentCamera` is a `Camera` Instance.

**Fails when:** workspace is `nil` or not an Instance.

---

## Test 4 — `RunService Methods`

Calls `game:GetService("RunService")` and checks `IsClient`, `IsServer`, `IsRunning`, `IsStudio` are all accessible. Verifies `RunService:IsClient()` returns `true` in a client context.

**Fails when:** RunService is missing or `IsClient()` returns `false` in a client executor.

---

## Test 94 — `Instance Index Error Fingerprint`

Accesses a property that doesn't exist on a `Part` (e.g., `part.DoesNotExist`). Checks the error starts with `"is not a valid member"`. Some executors produce different error messages.

**Warns when:** the error message doesn't match the standard Roblox format.

---

## Test 95 — `Instance Newproxy Identity`

Creates a `newproxy(true)` and checks `typeof` returns `"userdata"`. Then checks `typeof(Instance.new("Part"))` returns `"Instance"`. These must be distinct.

**Fails when:** `newproxy` userdata and Roblox Instances have the same `typeof`.

---

## Test 108 — `Self-Parenting Crash Test`

Tries to set `instance.Parent = instance`. Expects an error — Roblox doesn't allow self-parenting.

**Fails when:** self-parenting succeeds without error.

---

## Test 121 — `ContentProvider Preload Fakeout`

Calls `game:GetService("ContentProvider")` and verifies it's an Instance. Checks `PreloadAsync` exists as a function.

**Fails when:** ContentProvider is missing or `PreloadAsync` is not accessible.

---

## Test 122 — `TweenService PlaybackState Timing`

Creates a TweenInfo and a Tween. Checks the `PlaybackState` is `Enum.PlaybackState.Begin` before playing. Plays the tween and checks state changes.

**Warns when:** PlaybackState doesn't transition as expected.

---

## Test 123 — `FindPartOnRay Deprecated Trap`

Tries to call `workspace:FindPartOnRay(...)`. This method is deprecated in modern Roblox. Checks it either works or errors with a deprecation message — it should not crash the executor silently.

---

## Test 124 — `CollectionService Tag Replication`

Creates a Part, adds a tag with `CollectionService:AddTag`. Gets the tags back and checks the tag is present. Removes it and checks it's gone.

**Fails when:** tags aren't stored or don't survive read-back.

---

## Test 126 — `Service Identity Crisis (FindService vs GetService)`

Calls `game:GetService("Workspace")` and checks it returns the same object as `workspace`. Also compares `game:FindService("Workspace")` (deprecated) if available.

**Fails when:** `GetService("Workspace")` returns a different object than `workspace`.

---

## Test 127 — `StarterGui SetCore Error Fingerprint`

Calls `StarterGui:SetCore` with an invalid core name. Expects a `ResetButtonCallback`-related error, not a crash.

**Warns when:** the error format doesn't match standard Roblox error messages.

---

## Test 128 — `Teams Service Strict Child Check`

Gets `game:GetService("Teams")`. Checks it's an Instance. Adds a `Team` instance to it and reads it back.

**Fails when:** Teams service isn't accessible or doesn't accept Team instances.

---

## Test 129 — `UserInputService GetFocusedTextBox Validity`

Calls `UserInputService:GetFocusedTextBox()`. Without a focused box, it should return `nil`. Verifies the return type is either `nil` or an Instance.

**Fails when:** the function errors or returns a non-nil non-Instance.

---

## Test 130 — `Essential Services Presence Check`

Checks all of these services exist as Instances: `Players`, `Workspace`, `RunService`, `UserInputService`, `TweenService`, `Lighting`, `SoundService`, `StarterGui`, `StarterPack`, `StarterPlayer`.

**Fails when:** any service is missing.

---

## Test 131 — `DataModel Parent Lock (True Immutability)`

Tries `game.Parent = Instance.new("Folder")`. Expects an error — the DataModel's parent cannot be changed.

**Fails when:** the parent change succeeds without error.

---

## Test 132 — `PlayerScripts Container Validity`

Gets `Players.LocalPlayer.PlayerScripts` and checks it's an Instance. Verifies it's a descendant of `LocalPlayer`.

**Fails when:** `PlayerScripts` is nil or not in the expected hierarchy.

---

## Test 133 — `The Archivable Clone Trap`

Creates an Instance, sets `Archivable = false`, clones it. Expects `Clone()` to return `nil` when `Archivable` is false.

**Fails when:** `Clone()` returns an instance even when `Archivable == false`.

---

## Test 134 — `BindableFunction Recursive Deadlock`

Invokes a `BindableFunction` that tries to invoke itself again. Expects an error (deadlock protection).

**Fails when:** recursive BindableFunction invocation hangs the executor.

---

## Test 135 — `TextService GetTextSize Argument Fingerprint`

Calls `TextService:GetTextSize("Hello", 14, Enum.Font.GothamBold, Vector2.new(1000, 100))`. Checks the returned size is a `Vector2` with positive dimensions.

**Fails when:** `GetTextSize` is missing or returns non-Vector2.

---

## Test 145 — `RBXScriptSignal (Event) Identity`

Gets `workspace.ChildAdded` and checks `typeof` returns `"RBXScriptSignal"`. Connects two callbacks. Adds a child. Verifies both callbacks fire. Disconnects one. Adds another child. Verifies only the second fires.

**Fails when:** signal identity is wrong or disconnect doesn't work.

---

## Test 146 — `Instance Locked Metatable Message`

Tries `getmetatable(game)` — expects the returned guard string to be `"The metatable is locked"`.

**Warns when:** the guard string is different (executor may use a different message).

---

## Test 147 — `Enum Item Hierarchy & Cast`

Gets `Enum.Material.Plastic`. Checks `typeof` is `"EnumItem"`. Checks `item.EnumType == Enum.Material`. Checks `item.Value` is an integer. Checks `item.Name == "Plastic"`.

**Fails when:** any enum property is wrong.

---

## Test 148 — `RaycastParams Integrity Check`

Creates a `RaycastParams`, sets `FilterType` and adds instances to `FilterDescendantsInstances`. Reads them back and checks they match.

**Fails when:** RaycastParams properties don't persist correctly.

---

## Test 149 — `TextLabel Property Integrity & Computation Check`

Creates a `TextLabel` inside a `ScreenGui`. Sets `Text`, `TextSize`, and `Font`. Reads them back and checks consistency.

**Fails when:** TextLabel properties don't round-trip.

---

## Test 152 — `UIDragDetector Properties & Enums`

If `UIDragDetector` is available, creates one and checks its response style enum property. Validates the type is an `EnumItem`.

---

## Test 153 — `Workspace Blockcast (Physics Query)`

Calls `workspace:Blockcast(...)` with a test CFrame and size. Checks the return is either `nil` (no hit) or a `RaycastResult`-like table.

**Fails when:** `Blockcast` is missing or errors with valid arguments.

---

## Test 154 — `ProximityPrompt Exclusivity Default`

Creates a `ProximityPrompt`. Checks `Exclusivity` defaults to `Enum.ProximityPromptExclusivity.OneGlobally`.

---

## Test 156 — `OverlapParams Filter Validation`

Creates an `OverlapParams`. Sets `FilterDescendantsInstances` to a list of Instances. Reads it back and checks the count.

---

## Test 158 — `PathWaypoint Userdata Integrity`

Creates `PathWaypoint.new(Vector3.new(0,0,0), Enum.PathWaypointAction.Jump)`. Checks `typeof` is `"PathWaypoint"` and `.Position` is the expected Vector3.

---

## Test 159 — `TweenInfo Argument Reconstruction`

Creates `TweenInfo.new(1, Enum.EasingStyle.Quad, Enum.EasingDirection.Out, 2, true, 0.5)`. Reads back each field and verifies they match.

**Fails when:** any TweenInfo field is incorrect.

---

## Test 220 — `Instance.new ClassName Integrity`

Creates `Part`, `Folder`, `StringValue`, `NumberValue`, `BoolValue` with `Instance.new`. Verifies `ClassName` matches and `typeof` is `"Instance"`.

**Fails when:** ClassName is wrong or the created instance isn't an Instance.

---

## Test 221 — `Instance Property Type Enforcement`

Tries to set `Part.Transparency = "not_a_number"`. Expects an error. Sets `Part.Name = 12345` — may coerce, but if it succeeds, the name must be a string.

**Fails when:** non-numeric transparency is accepted silently.

---

## Test 222 — `GetChildren Return Type`

Creates a `Folder`, adds one `Part` child, calls `:GetChildren()`. Checks return is a table, length is 1, and the child is the correct instance.

**Fails when:** `GetChildren` returns wrong type or count.

---

## Test 223 — `Instance IsA Hierarchy Chain`

Checks `Part:IsA("Part")`, `:IsA("BasePart")`, `:IsA("PVInstance")`, `:IsA("Instance")` all return true. Checks `:IsA("Folder")` returns false.

**Fails when:** any IsA result is wrong.

---

## Test 224 — `FindFirstChild Recursive vs Direct`

Creates `root -> mid -> DeepPart`. Direct search on `root` for `"DeepPart"` should return `nil`. Recursive search should return the part.

**Fails when:** direct search finds nested children, or recursive doesn't.

---

## Test 225 — `Instance GetFullName Accuracy`

Creates a `Folder` named `"TestFolder"` in `workspace`. Gets `GetFullName()` and checks it contains both `"TestFolder"` and `"Workspace"`.

**Fails when:** GetFullName doesn't include the full path.

---

## Test 226 — `WaitForChild Timeout Behavior`

Calls `folder:WaitForChild("NonExistent", 0.1)`. Checks it returns `nil` and doesn't take more than 1 second.

**Fails when:** WaitForChild hangs or doesn't return nil on timeout.

---

## Test 227 — `Destroyed Instance Property Access`

Destroys a Part. Tries to read `.Parent` — should be `nil`. Tries to set `.Parent = workspace` — should error.

**Fails when:** destroyed instances still accept parenting.

---

## Test 238 — `Signal Connection Disconnect`

Connects to a `BindableEvent`, fires it, then disconnects. Fires again. Checks the callback only ran once.

**Fails when:** the signal still fires after disconnect.

---

## Test 239 — `Signal Once Single-Fire Guarantee`

Connects with `:Once(...)`. Fires the event twice. Checks the callback only ran once.

**Fails when:** the Once-connected callback fires more than once.

---

## Test 240 — `BindableEvent Argument Passthrough`

Fires a `BindableEvent` with a nested table. Checks the receiver gets the same table structure.

**Fails when:** tables are serialized/stripped during BindableEvent transmission.

---

## Test 280 — `Instance.new Invalid Class`

Calls `Instance.new("NotARealClassName12345")`. Expects an error.

**Fails when:** an invalid class name is silently accepted.

---

## Test 281 — `GetPropertyChangedSignal Fires`

Connects to `Part:GetPropertyChangedSignal("Name")`. Changes the name. Waits. Checks the callback fired.

**Fails when:** `GetPropertyChangedSignal` doesn't fire on the expected property.

---

## Test 282 — `GetDescendants Depth Check`

Creates `root -> child -> grandchild`. `:GetChildren()` returns 1. `:GetDescendants()` returns 2.

**Fails when:** descendants count is wrong.

---

## Test 283 — `LocalPlayer Existence Check`

Gets `Players.LocalPlayer`. If not nil, checks it's an Instance, `IsA("Player")`, and has a numeric `UserId`.

**Warns when:** LocalPlayer is nil (may indicate the script context isn't a LocalScript).

---

## Test 284 — `LocalPlayer Character Validation`

If `LocalPlayer.Character` exists, checks it's a `Model` and a descendant of `workspace`.

**Fails when:** Character exists but isn't in workspace.

---

## Test 285 — `GetService Invalid Name Error`

Calls `game:GetService("NotARealService12345")`. Expects an error.

**Fails when:** invalid service names are silently accepted.

---

## Test 286 — `Workspace Gravity Default`

Checks `workspace.Gravity` is a positive number.

**Fails when:** Gravity is not a number or is non-positive.

---

## Test 287 — `Lighting ClockTime Range`

Checks `Lighting.ClockTime` is a number in range `[0, 24)`.

**Fails when:** ClockTime is out of range.

---

## Test 288 — `SoundService Property Check`

Checks `SoundService` exists and `AmbientReverb` is an `EnumItem`.

**Fails when:** SoundService is missing or the property has wrong type.

---

## Test 289 — `Enum GetEnumItems Check`

Calls `Enum.Material:GetEnumItems()`. Checks return is a non-empty table of `EnumItem` values.

**Fails when:** the list is empty or contains non-EnumItems.

---

## Test 290 — `EnumItem Value Integer Check`

Checks `Enum.Material.Plastic.Value` is an integer (no fractional part).

**Fails when:** enum values are floats or strings.

---

## Test 291 — `Instance Attribute Round-Trip`

Sets number, string, and bool attributes. Reads them back. Checks a missing attribute returns `nil`.

**Fails when:** any attribute type is lost or wrong.

---

## Test 292 — `Instance GetAttributes All`

Sets two attributes on a Part. Calls `:GetAttributes()`. Checks both keys are present in the returned table.

**Fails when:** `GetAttributes` misses attributes.

---

## Test 311 — `Rawequal Instance Identity`

Calls `game:GetService("Workspace")` twice. Checks both references are `rawequal`.

**Fails when:** the same service returns different object references across calls.

---

## Test 321 — `Instance Clone Deep Copy`

Creates `parent -> child`. Clones parent. Checks the clone has a child named the same, and the clone's child is a different object from the original.

**Fails when:** Clone doesn't deep-copy children.

---

## Test 322 — `Instance ClearAllChildren`

Creates a Folder with 5 children. Calls `ClearAllChildren`. Checks `GetChildren()` returns 0.

**Fails when:** children remain after ClearAllChildren.

---

## Test 323 — `BindableFunction Invoke Return Value`

Sets `OnInvoke = function(x) return x * 2 end`. Invokes with `21`. Checks `result == 42`.

**Fails when:** return value is wrong.

---

## Test 324 — `Multiple Event Connections All Fire`

Connects 3 callbacks to the same event with increments of `+1`, `+10`, `+100`. Fires once. Checks total is `111`.

**Fails when:** not all connections fire.

---

## Test 346 — `Workspace Terrain Instance`

Checks `workspace.Terrain` exists, `IsA("Terrain")`, and its Parent is `workspace`.

---

## Test 353 — `IsDescendantOf Check`

Creates a 3-level hierarchy. Checks `c:IsDescendantOf(b)`, `c:IsDescendantOf(a)` are true. `a:IsDescendantOf(c)` is false.

---

## Test 354 — `IsAncestorOf Check`

Checks `a:IsAncestorOf(b)` is true. `b:IsAncestorOf(a)` is false.

---

## Test 355-359 — `Value Instance Round-Trips`

Tests `StringValue`, `NumberValue`, `BoolValue`, `ObjectValue`, and `IntValue` for correct property round-trips.

- **StringValue**: assigns `"TestString"` and empty string
- **NumberValue**: assigns `3.14159` and `-999`
- **BoolValue**: toggles between `false` and `true`
- **ObjectValue**: assigns a Part reference, then `nil`
- **IntValue**: assigns `42` (integer) and `3.7` (float, expects truncation)

---

## Test 371 — `Typeof Roblox Datatype Coverage`

Checks `typeof` on `Vector3`, `Vector2`, `CFrame`, `Color3`, `UDim2`, `UDim`, and `Ray`. Each must return the correct type name string.

---

## Test 372-377 — `Service Existence Checks`

Checks that `RunService`, `MarketplaceService`, `ReplicatedStorage`, `StarterGui`, `GuiService`, and `PhysicsService` all exist as Instances.

---

## Test 381 — `Sound Instance Default Properties`

Creates a `Sound`. Checks `Volume` is in `[0,1]`, `Playing == false`, and `SoundId` is a string.

---

## Test 382-383 — `Part Enum Property Checks`

- **Material**: checks default is `EnumItem`, assigns `Enum.Material.Neon`, reads back
- **Shape**: checks default is `EnumItem`, assigns `Enum.PartType.Ball`, reads back

---

## Test 384 — `FallenPartsDestroyHeight Check`

Checks `workspace.FallenPartsDestroyHeight` is a number.

---

## Test 385-386 — `Game Creator Properties`

- **CreatorId**: must be a number
- **CreatorType**: must be an `EnumItem`

---

## Test 388 — `Debris AddItem Functionality`

Gets `Debris` service, creates a Part, calls `Debris:AddItem(part, 60)`. Checks no error is thrown.

---

## Test 392 — `Enum GetEnums Global Check`

Calls `Enum:GetEnums()`. Checks more than 100 enum types are returned (Roblox has hundreds).

---

## Test 393 — `EnumItem Tostring Format`

Checks `tostring(Enum.Material.Plastic) == "Enum.Material.Plastic"`.

---

## Test 394 — `Instance.new With Parent Argument`

Creates `Instance.new("Part", folder)`. Checks `part.Parent == folder` and `#folder:GetChildren() == 1`.

---

## Test 395 — `Game FindFirstChild Non-Existent`

Calls `game:FindFirstChild("NonExistentService99999")`. Expects `nil`.

---

## Test 396 — `Instance Name Special Characters`

Sets `f.Name = "Test/Folder.With-Special_Chars!@#"` and reads it back.

---

## Test 400 — `Instance Tostring Format`

Creates a Part named `"MyPart"`. Checks `tostring(part) == "MyPart"`.

---

## Test 408-409 — `Game & Workspace ClassName`

- `game.ClassName == "DataModel"`
- `workspace.ClassName == "Workspace"`

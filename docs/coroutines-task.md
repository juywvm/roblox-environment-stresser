# Coroutines & Task Scheduler

These tests cover coroutine lifecycle, yield semantics, cross-boundary yields, the `task` library, and scheduler ordering. A broken scheduler is one of the most common sources of executor bugs.

---

## Test 3 — `Task Library Behavior`

Checks that `task.wait`, `task.spawn`, `task.defer`, `task.delay`, and `task.cancel` all exist as functions. Then calls `task.wait(0)` and verifies it returns a number (elapsed time).

**Fails when:** any task function is missing, or `task.wait` returns a non-number.

---

## Test 36 — `Coroutine Status Cycle`

Creates a coroutine that yields once. Checks the status at each stage:
- Before first resume: `"suspended"`
- While running (from inside): `"running"`
- After yield: `"suspended"`
- After final resume: `"dead"`

**Fails when:** any status value is wrong or out of order.

---

## Test 38 — `Main Thread & Yieldability Check`

Calls `coroutine.running()` from the main thread. In Lua 5.2+, this returns the main thread and `true`. Verifies the return type is a thread.

**Fails when:** `coroutine.running()` returns `nil` or a non-thread value from the main thread.

---

## Test 39 — `Dead Thread Resurrection`

Tries to resume a dead coroutine. Expects `false` as the first return value and an error message mentioning "cannot resume dead coroutine".

**Fails when:** a dead coroutine accepts a resume without error.

---

## Test 40 — `task.spawn Error Isolation`

Spawns a task with `task.spawn` that immediately errors. Verifies the error does not propagate to the caller — `task.spawn` should isolate errors in spawned threads.

**Fails when:** the error from the spawned function crashes the calling context.

---

## Test 41 — `Multi-Value Yield Pipeline`

Creates a coroutine that yields multiple values, gets resumed with multiple values, and returns multiple values. Checks that all values pass through each stage correctly.

**Fails when:** values are dropped, reordered, or coerced at any stage.

---

## Test 47 — `coroutine.wrap Error Propagation`

Uses `coroutine.wrap` to create a generator. The inner function errors. Calls the wrapper and expects the error to propagate through to the caller.

**Fails when:** `coroutine.wrap` swallows errors instead of propagating them.

---

## Test 92 — `Coroutine Yield Boundary`

Tests that yielding from inside a `pcall` works (Luau allows this, vanilla Lua doesn't in some versions). Yields inside `pcall`, resumes, and checks the `pcall` return values are intact.

**Fails when:** yielding inside `pcall` crashes or produces wrong results.

---

## Test 107 — `Coroutine Resume Argument Tunneling`

Passes arguments into a coroutine on both the first resume and subsequent resumes. Verifies that:
- First resume args arrive as function parameters
- Later resume args arrive as return values of `coroutine.yield`

**Fails when:** resume arguments don't map correctly to yield return values.

---

## Test 110 — `Coroutine Wrapper Stack Trace`

Uses `coroutine.wrap` and triggers an error from inside. Verifies the error message includes a meaningful stack trace (not just "attempt to call nil").

**Warns when:** the stack trace is empty or missing file/line info, which is a sign of stripped debug info.

---

## Test 179 — `Coroutine Pool Reuse Prevention`

Creates 5 coroutines, runs them all to completion. Then tries to resume each one again and checks the resume returns `false`. Dead threads must not be reused.

**Fails when:** a dead coroutine accepts a second resume.

---

## Test 180 — `Nested Coroutine Yield Chain (3 Deep)`

Creates `outer -> middle -> inner`. `inner` yields `"L3"`. `middle` resumes `inner`, prepends `"L2_"`, and yields. Reads from `outer` and checks the concatenated value at each stage.

**Fails when:** values are not correctly propagated up through 3 levels of nesting.

---

## Test 181 — `Yield Across pcall Boundary`

Yields from inside a `pcall`. Resumes the coroutine. Verifies that after resume, the `pcall` completes successfully and its return values are correct.

**Fails when:** the yield breaks the `pcall` context or produces wrong results.

---

## Test 182 — `Coroutine.running() Identity`

Creates a coroutine. From inside, calls `coroutine.running()` and stores the result. After the coroutine finishes, checks that the stored value equals the coroutine object itself.

**Fails when:** `coroutine.running()` returns a different thread than the currently running one.

---

## Test 183 — `Coroutine Resume Error Propagation`

Creates a coroutine that calls `error("Intentional")`. Resumes it and checks:
- Resume returns `false`
- The error message contains `"Intentional"`

**Fails when:** the error is swallowed or the message is empty.

---

## Test 184 — `Coroutine Wrap State Isolation`

Uses `coroutine.wrap` to create a generator that yields 1, 2, 3. Calls it three times and checks each yield. Calls it a fourth time and expects an error (the thread is exhausted).

**Fails when:** the fourth call returns `nil` silently instead of erroring, or the yield sequence is wrong.

---

## Test 185 — `Symmetric Coroutine Ping-Pong`

Creates two coroutines where `co_b` resumes `co_a` from inside itself. Logs execution order into a shared table. Verifies that all four log entries exist in the correct order.

**Fails when:** the scheduler breaks mutual yields or drops a log entry.

---

## Test 186 — `Asymmetric Yield Value Counts`

Yields 1 value, then 2, then 3 in a single coroutine. Each resume reads back the yield results and checks the actual return count from `coroutine.resume` is correct (n+1 including the `ok` bool).

**Fails when:** the return count doesn't match the yield count.

---

## Test 187 — `Coroutine Close Behavior`

If `coroutine.close` exists, creates a suspended coroutine and closes it. Checks the status becomes `"dead"` and the close returns `true`.

**Fails when:** `coroutine.close` doesn't mark the thread as dead.

---

## Test 217 — `Task.defer Ordering`

Defers a task with `task.defer`. Then inserts `"immediate"` into a log. After a `task.wait()`, checks that `"immediate"` came first and `"deferred"` came second.

**Fails when:** the deferred callback fires before the current scope finishes.

---

## Test 218 — `Task.delay Precision`

Schedules a callback with `task.delay(0.1, fn)`. Waits 0.3 seconds. Checks the callback fired.

**Fails when:** the callback never fires, or fires before the delay expires.

---

## Test 219 — `Task.cancel Prevention`

Schedules a callback with `task.delay(0.1, fn)`. Immediately cancels it. Waits 0.3 seconds. Checks the callback never ran.

**Fails when:** the callback fires despite being cancelled.

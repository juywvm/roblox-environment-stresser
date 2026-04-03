# Roblox Executor Environment Stress Test Suite

It is a comprehensive stress test script targeting the Roblox **Client** executor environment. It runs **410 tests** across **18 categories**, each designed to expose inconsistencies, proxy artifacts, hooked APIs, and emulation bugs in custom/executor environments.

## How It Works

The script uses a central `RunTest(name, fn)` harness that:
- Wraps each test in `pcall` so a failing test never stops execution
- Logs `PASS` / `FAIL` with the error message for every test
- Prints a final summary with total pass/fail counts and a `PERFECT SCORE` or failure conclusion

## Test Naming

Every test has an ID assigned at runtime in the format `[XXXX]`, e.g. `[0001]`. The order is deterministic — same executor, same run order.

## Skipping Tests

Many tests check for the existence of an API before running (e.g., `if not buffer then return end`). These count as **PASS** if the API doesn't exist — they're designed to be informational, not mandatory.

## Pass vs Fail

- **PASS** — the test ran and all assertions held, or the test gracefully skipped (API missing).
- **FAIL** — an `assert`, `error`, or unexpected behavior triggered an error inside the test function.

## Running

Run the script inside a Roblox Environment. The output will be printed.

```lua
-- Expected final output
[CONCLUSION] PERFECT SCORE. The environment is accurate.
-- or
[CONCLUSION] The environment has FAILED integrity checks.
```

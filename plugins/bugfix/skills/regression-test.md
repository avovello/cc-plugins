---
name: regression-test
description: How to write a good regression test for a bug — what to cover, naming conventions, where to place it
---

# Writing Regression Tests

Guide for writing tests that prevent a fixed bug from recurring.

## What Makes a Good Regression Test

1. **Reproduces the original bug** — the test would fail before the fix
2. **Tests the exact scenario** — not a general test, but the specific input/state that triggered the bug
3. **Includes edge cases** — variations of the triggering condition
4. **Is clearly named** — references the bug or describes the scenario

## Naming Convention

Name tests to describe the bug scenario, not the fix:

```
# Good — describes the scenario
test_upload_handles_files_larger_than_5mb
test_login_with_special_characters_in_password
test_concurrent_writes_do_not_corrupt_state

# Bad — describes the fix
test_fix_upload_bug
test_patched_login
```

## Test Structure

```
1. Arrange — set up the exact conditions that triggered the bug
2. Act — perform the action that caused the failure
3. Assert — verify correct behavior (not just "doesn't crash")
```

## What to Cover

### The Exact Bug
- The specific input that caused the failure
- The specific state/configuration that triggered it

### Boundary Conditions
- Just below and just above any thresholds involved
- Empty, null, and maximum-size inputs if relevant

### Related Scenarios
- Similar inputs that might trigger the same class of bug
- Different code paths that share the same vulnerable pattern

## Where to Place the Test

1. Find existing test files for the module: `Glob("**/test*[module_name]*")`
2. Place the regression test near related tests
3. If no test file exists, create one following the project's test file conventions

## When to Delegate

For complex test scenarios (mocking, fixtures, integration setup), use `qa:test-writer`:

```
Task(subagent_type="qa:test-writer", prompt="Write regression tests for [bug description] in [module]. The fix was [description]. Cover: [scenarios]")
```

Use `qa:test-runner` to execute:

```
Task(subagent_type="qa:test-runner", prompt="Run tests in [test file]")
```

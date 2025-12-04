# Testing Loop Skill

## Purpose

Provides the test-fix-retest loop capability for automated test failure resolution.

## When to Load

Load this skill when:
- Running tests after implementation
- Test failures need to be analyzed and fixed
- Implementing the QA-DEV-REVIEW loop

## The Testing Loop

```
1. Run Tests
   ↓
2. Parse Results
   ↓
3. Tests Pass? → Done ✅
   ↓ (if fail)
4. Analyze Failures
   ↓
5. Identify Root Cause
   ↓
6. Apply Fix
   ↓
7. Back to Step 1 (max 3 iterations)
```

## Implementation

### Step 1: Run Tests

```bash
# JavaScript/TypeScript
npm test

# Python
pytest --tb=short

# PHP
vendor/bin/phpunit

# Go
go test ./...
```

### Step 2: Parse Results

**Extract Key Information**:
- Test name and file location
- Error message
- Stack trace
- Expected vs actual values

**Example Failure**:
```
FAIL src/services/PaymentService.test.ts
  ● PaymentService › createPayment › should handle timeout

    Timeout - Async callback was not invoked within 5000ms

      at src/services/PaymentService.test.ts:45:5
```

### Step 3: Analyze Failure Categories

| Category | Symptoms | Typical Fix |
|----------|----------|-------------|
| Missing await | Timeout, undefined | Add await |
| Wrong assertion | Expected X, got Y | Fix expectation or implementation |
| Missing mock | X is not a function | Add mock |
| Race condition | Intermittent failure | Add proper async handling |
| Type mismatch | Type error | Fix types |

### Step 4: Apply Fix

**Implementation Bug**: Fix the source code
**Test Bug**: Fix the test code
**Missing Mock**: Add the mock setup

### Step 5: Loop Control

```markdown
## Iteration Tracking

### Iteration 1
- Failures: 3
- Fixed: 2
- Remaining: 1

### Iteration 2
- Failures: 1
- Fixed: 1
- Remaining: 0

### Result: ✅ All tests pass
```

**Max Iterations**: 3 (prevent infinite loops)

## Coverage Requirements

**Target**: 80%+ coverage

```bash
# Generate coverage report
npm test -- --coverage

# Check thresholds
npx jest --coverage --coverageThreshold='{"global":{"lines":80}}'
```

## Integration with Feature Plugin

This skill is used in Phase 6 (Quality Review) of the feature-dev command:

```markdown
## Phase 6: Quality Review

### Part A: Testing Loop
1. Write tests (test-writer behavior)
2. Run tests
3. If failures: Analyze → Fix → Rerun (max 3 iterations)
4. When all pass: Continue to Review Loop
```

## Best Practices

1. **Fix One at a Time**: Address one failure, verify fix, then next
2. **Understand Root Cause**: Don't guess, trace the actual problem
3. **Check Side Effects**: Ensure fix doesn't break other tests
4. **Track Iterations**: Know when to stop and ask for help
5. **Preserve Existing Tests**: Don't delete tests just because they fail

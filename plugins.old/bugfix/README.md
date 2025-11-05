# Bugfix Plugin 🐛

Intelligent bug fixing with comprehensive root cause analysis and testing loops.

## Overview

The Bugfix Plugin implements a systematic approach to bug fixing that emphasizes root cause analysis, comprehensive testing, and verification to prevent regressions.

## Workflow Diagram

```
┌──────────────────┐
│  Reproduce Bug   │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│   Analyze Bug    │
└────────┬─────────┘
         │
         ▼
┌──────────────────────┐
│ Root Cause Analysis  │
└────────┬─────────────┘
         │
         ▼
┌──────────────────┐
│    Plan Fix      │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  Implement Fix   │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│   Unit Tests     │◄──────────┐
└────────┬─────────┘           │
         │ passed?              │
         │ no                   │
         ├──────┐               │
         │      ▼               │
         │  ┌─────────┐        │
         │  │Re-analyze│        │
         │  └────┬────┘        │
         │       ▼              │
         │  ┌────────┐         │
         │  │ Re-fix │─────────┘
         │  └────────┘
         │ yes
         ▼
┌──────────────────┐
│ Regression Tests │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│   Code Review    │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│    Document      │
└──────────────────┘
```

## Processing Loops

### 1. Reproduction Loop
- **Try to Reproduce** → (if can't reproduce) **Request More Info** → **Try Again**
- Ensures bug is understood before attempting fix

### 2. Fix-Test Loop
- **Implement Fix** → **Test** → (if fails) **Re-analyze** → **Adjust Fix** → **Re-test**
- Repeats until fix works correctly
- Maximum 3 iterations

### 3. Regression Test Loop
- **Run Full Test Suite** → (if regressions) **Fix Regressions** → **Re-test**
- Ensures fix doesn't break anything else

## Phases

### Phase 1: Bug Reproduction
1. **Understand Bug Report**
   - Read bug description
   - Identify affected functionality
   - Note steps to reproduce

2. **Reproduce Bug**
   - Follow reproduction steps
   - Verify bug exists
   - Document actual vs expected behavior

3. **Create Reproduction Test**
   - Write failing test that demonstrates bug
   - Ensure test is reliable
   - Document edge cases

### Phase 2: Root Cause Analysis
1. **Analyze Bug**
   - Examine error messages and stack traces
   - Read relevant source code
   - Trace execution flow

2. **Identify Root Cause**
   - Find the exact line(s) causing the issue
   - Understand WHY it's failing
   - Distinguish symptom from cause

3. **Assess Impact**
   - Determine affected components
   - Identify similar bugs that might exist
   - Assess severity

### Phase 3: Fix Planning
1. **Design Fix**
   - Plan the specific changes needed
   - Consider alternative approaches
   - Identify potential side effects

2. **Review Plan**
   - Is this the minimal fix?
   - Does it address root cause?
   - Will it prevent future occurrences?

### Phase 4: Fix Implementation (with Testing Loop)
1. **Implement Fix**
   - Make the planned changes
   - Follow coding standards
   - Add defensive checks if needed

2. **Unit Test**
   - Run reproduction test
   - Verify it now passes
   - Run related unit tests

3. **Re-analyze and Re-fix** (if tests fail)
   - Analyze why fix didn't work
   - Adjust approach
   - Re-test

4. **Repeat** until tests pass (max 3 times)

### Phase 5: Regression Testing
1. **Full Test Suite**
   - Run complete test suite
   - Identify any new failures

2. **Fix Regressions** (if any)
   - Address any regressions caused by fix
   - Adjust fix to avoid breaking changes

### Phase 6: Code Review
- Review fix quality
- Ensure best practices followed
- Check for edge cases

### Phase 7: Documentation
- Update CHANGELOG
- Add code comments explaining fix
- Document why bug occurred (for learning)

## Subagents

- **Bug Reproducer**: Reproduces bugs reliably
- **Root Cause Detective**: Identifies true root causes
- **Fixer**: Implements fixes carefully
- **Tester**: Executes comprehensive tests
- **Regression Tester**: Ensures no new bugs introduced

## Configuration

```yaml
# .claude/bugfix-config.yaml
bugfix:
  reproduction:
    max_attempts: 3
    require_test: true

  analysis:
    depth: deep # surface | moderate | deep

  fixing:
    max_fix_iterations: 3
    require_unit_tests: true

  regression:
    run_full_suite: true
    fail_on_regression: true

  review:
    require_review: true
```

## Usage

### Via Slash Command
```bash
/bugfix "Users can't log in with special characters in password"
```

### Via Workflow
```bash
claude-code workflow bugfix \
  --bug-id="GH-123" \
  --description="Login fails with special characters"
```

## Hooks

### Available Hooks
- `pre-reproduction`: Before attempting to reproduce
- `on-reproduction-failure`: When can't reproduce bug
- `post-root-cause`: After root cause identified
- `pre-fix`: Before implementing fix
- `on-test-failure`: When fix tests fail
- `post-fix-success`: When fix tests pass
- `on-regression`: When regression detected
- `post-bugfix-complete`: When entire workflow complete

## Success Criteria

Bugfix is complete when:
- [x] Bug reproduced reliably
- [x] Root cause identified
- [x] Fix implemented
- [x] Reproduction test passes
- [x] No regressions introduced
- [x] Code reviewed
- [x] Documentation updated

## Examples

See `examples/bugfix/` for detailed examples:
- Simple bug fix (syntax error)
- Logic bug requiring investigation
- Bug with subtle edge case
- Bug requiring refactoring
- Bug with performance implications

---
description: Systematic bug fixing with reproduction, root cause analysis, fix implementation, and regression testing
---

# Bugfix Command

**Purpose**: Systematic bug fixing with reproduction, root cause analysis, fix implementation, and regression testing

## Workflow

1. **Bug Reproduction**: Reproduce the bug reliably
2. **Root Cause Analysis**: Identify the exact cause
3. **Impact Assessment**: Assess severity and affected areas
4. **Fix Planning**: Plan the fix approach
5. **Fix Implementation**: Implement the fix
6. **Testing Loop**: Test → (if fail) → Re-analyze → Re-fix → Re-test (max 3 iterations)
7. **Regression Testing**: Run full test suite

## Specialized Agents (7)
- bug-reproducer: Reproduces bugs and creates failing tests
- root-cause-analyst: Identifies root causes
- impact-assessor: Assesses impact and severity
- fix-planner: Plans fix approach
- fix-implementer: Implements fixes
- fix-tester: Tests fixes
- regression-tester: Runs regression tests

## Usage
```bash
/bugfix "Login fails with special characters in password"
```

## Processing Loop
Fix → Test → (if fail) → Re-analyze → Re-fix → Re-test (max 3 iterations)

## Version
1.0.0

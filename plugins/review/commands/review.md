---
description: Comprehensive code review covering architecture, security, performance, and language-specific concerns
---

# Code Review Command

Review code changes for architecture violations, security flaws, performance issues, and language-specific concerns. Reports only findings with confidence >= 80.

## Usage

```bash
/review                    # Review current uncommitted changes
/review src/auth/*.js      # Review specific files
```

## Workflow

### Step 1: Identify Changes

```bash
git diff --name-only
```

### Step 2: Review

```
Task(subagent_type="review:code-reviewer", prompt="Review these changes: [diff summary]")
```

The code-reviewer detects what's relevant from the diff and applies appropriate perspectives:
- **Architecture**: Pattern violations, service boundaries, dependency flow
- **Security**: Injection, auth flaws, input validation, secrets
- **Performance**: N+1 queries, missing indexes, memory leaks
- **Language-specific**: Idiomatic patterns, framework best practices, type safety
- **DevOps**: Dockerfile, CI/CD, k8s config

### Step 3: Report

```markdown
# Code Review Report

**Status**: APPROVED / NEEDS WORK / BLOCKED

## Summary
- Files Reviewed: X
- Issues Found: Y (Critical: 0, High: Z, Medium: W)

## Issues by Severity
[Grouped findings with file:line, confidence, fix suggestions]
```

Only issues with confidence >= 80 are reported. Grouped by severity: critical, high, medium.

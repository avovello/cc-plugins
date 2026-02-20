---
description: Systematic bug fixing with root cause analysis and fix implementation
---

# Bugfix Command

Investigate a bug report, identify the root cause, and implement a fix.

## Usage

```bash
/bugfix "Login fails with special characters in password"
```

## Workflow

### Step 1: Root Cause Analysis

```
Task(subagent_type="bugfix:root-cause-analyst", prompt="Investigate: [bug description]")
```

The root-cause-analyst will:
- Reproduce the bug (create failing test if possible)
- Trace execution path to root cause
- Assess impact scope
- Plan the fix approach

### Step 2: Fix Implementation

```
Task(subagent_type="bugfix:fix-implementer", prompt="Fix based on analysis: [root cause analysis results]")
```

The fix-implementer will:
- Apply code changes per the fix plan
- Update affected tests
- Verify lint/type checks pass

### Step 3: Verify

Run the failing test or reproduction steps to confirm the fix works. If the fix doesn't resolve the issue, return to Step 2 with updated analysis (max 3 iterations).

## Agents

| Agent | Purpose |
|-------|---------|
| `bugfix:root-cause-analyst` | Reproduce, trace, assess, plan |
| `bugfix:fix-implementer` | Apply code changes |

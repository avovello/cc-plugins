# Review Plugin

Two-phase code review: investigate first, then review in parallel.

## Overview

The Review plugin splits code review into two phases:

1. **Investigation** — One investigator agent maps out changes, traces dependencies, and produces a structured report
2. **Review** — Four specialized reviewers analyze the report in parallel, each focused on a single domain

## Installation

```bash
/plugin install https://github.com/avovello/cc-plugins.git#plugins/review
```

## Usage

```bash
/review                        # Review uncommitted changes (default)
/review --commit abc123        # Review a specific commit
/review --commit HEAD~3..HEAD  # Review a commit range
/review --pr 123               # Review a pull request
```

## Architecture

### Phase 1: Investigation (1 agent)

| Agent | Tools | Input Source |
|-------|-------|-------------|
| `diff-investigator` | Bash, Read, Glob, Grep | `git diff` (uncommitted changes) |
| `commit-investigator` | Bash, Read, Glob, Grep | `git show` (specific commit) |
| `pr-investigator` | Bash, Read, Glob, Grep | `gh pr diff` (pull request) |

One investigator launches based on the review mode. All three produce the same structured **Investigation Report** containing:
- Changed files with categories and diffs
- Dependency map (callers, callees, interfaces)
- Context tags (security-sensitive, DB-related, API boundary)

### Phase 2: Parallel Review (4 agents)

| Agent | Tools | Focus |
|-------|-------|-------|
| `security-reviewer` | Read, Grep | Injection, auth, secrets, input validation, crypto |
| `architecture-reviewer` | Read, Glob, Grep | Dependencies, layers, coupling, boundaries, patterns |
| `performance-reviewer` | Read, Grep | N+1 queries, indexes, O(n²), resource leaks, caching |
| `bug-reviewer` | Read, Grep | Logic errors, type issues, error handling, data integrity |

All 4 launch in parallel. Each receives the Investigation Report and performs targeted analysis in its domain. Each only reports issues with **confidence >= 80**.

### Phase 3: Validation & Report

Issues from Phase 2 are validated by lightweight agents to filter false positives. The final report groups findings by severity.

## Tool Restrictions

Agents have the minimum tools needed for their job:

- **Investigators** get `Bash` for git commands + `Read, Glob, Grep` for exploration
- **Architecture reviewer** gets `Read, Glob, Grep` — needs Glob to verify project structure
- **Security, Performance, Bug reviewers** get `Read, Grep` — focused on pattern matching in specific files
- **No agent has Write or Edit** — review is read-only

## Quality Gates

| Severity | Threshold | Result |
|----------|-----------|--------|
| Critical | > 0 | BLOCKED |
| High | > 2 | NEEDS WORK |
| Medium | > 10 | NEEDS WORK |
| All within thresholds | — | APPROVED |

## Output

```
# Code Review Report

**Status**: APPROVED / NEEDS WORK / BLOCKED

## Summary
- Files Reviewed: X
- Issues Found: Y (Critical: 0, High: Z, Medium: W)

## Issues by Severity
[Grouped findings with file:line, confidence, fix suggestions]
```

Only issues with confidence >= 80 are reported.

## Version

2.1.0

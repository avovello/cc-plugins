---
description: Code review with focused investigation and parallel specialized reviewers
allowed-tools: Task, Bash(git diff:*), Bash(git show:*), Bash(git log:*), Bash(git diff-tree:*), Bash(gh pr view:*), Bash(gh pr diff:*)
---

# Code Review Command

Review code changes using a team of specialized agents: an investigator maps out the changes, then 4 focused reviewers analyze in parallel.

**Agent assumptions (applies to all agents and subagents):**
- All tools are functional. Do not test tools or make exploratory calls.
- Only call a tool if it is required to complete the task.
- Do NOT generate commits, push code, or modify any files. This is a read-only review.

## Usage

```
/review                        # Review uncommitted changes (default)
/review --commit <sha>         # Review a specific commit
/review --commit HEAD~3..HEAD  # Review a commit range
/review --pr <number>          # Review a pull request
```

## Workflow

### Step 1: Determine Review Mode

Parse arguments to determine the review mode:

- No arguments or file paths → **diff mode** (uncommitted changes)
- `--commit <ref>` → **commit mode**
- `--pr <number>` → **PR mode**

### Step 2: Early Exit Check

Launch a haiku agent to verify there is something to review:

- **diff mode**: Run `git diff --name-only` and `git diff --cached --name-only`. If both are empty, stop: "No uncommitted changes to review."
- **commit mode**: Run `git show --stat <ref>`. If the commit doesn't exist or has no changes, stop.
- **pr mode**: Run `gh pr view <number> --json state`. If the PR is closed or merged, stop: "PR is already closed/merged."

If nothing to review, stop and report to the user.

### Step 3: Investigation (Phase 1)

Launch the appropriate investigator agent based on review mode:

- **diff mode**: `Task(subagent_type="review:diff-investigator", prompt="Investigate uncommitted changes.")`
- **commit mode**: `Task(subagent_type="review:commit-investigator", prompt="Investigate commit: <ref>")`
- **pr mode**: `Task(subagent_type="review:pr-investigator", prompt="Investigate PR #<number>")`

The investigator returns a structured **Investigation Report** with:
- All changed files with categories, diffs, and dependencies
- Context about security-sensitive areas, DB access, API boundaries
- Dependency map showing how changes relate

### Step 4: Parallel Review (Phase 2)

Launch all 4 review agents **in parallel**, each receiving the full Investigation Report:

```
Task(subagent_type="review:security-reviewer", prompt="Review this investigation report for security issues:\n\n<report>[investigation report]</report>")
Task(subagent_type="review:architecture-reviewer", prompt="Review this investigation report for architecture issues:\n\n<report>[investigation report]</report>")
Task(subagent_type="review:performance-reviewer", prompt="Review this investigation report for performance issues:\n\n<report>[investigation report]</report>")
Task(subagent_type="review:bug-reviewer", prompt="Review this investigation report for bugs and logic errors:\n\n<report>[investigation report]</report>")
```

Each reviewer returns issues with confidence scores >= 80.

### Step 5: Validate Findings

For each issue found by the reviewers in Step 4, launch a **parallel validation agent** (haiku model) to verify the issue is real:

- Provide the issue description, file path, and the relevant code
- The validator must confirm the issue exists in the actual code
- For bugs: verify the logic error is real, not a misread of the code
- For security: verify the vulnerability is exploitable, not mitigated elsewhere
- For architecture: verify the pattern violation exists, not an intentional design choice
- For performance: verify the bottleneck is real, not already optimized

Filter out any issues that fail validation. This gives the final list of **high-signal issues**.

**CRITICAL: We only want HIGH SIGNAL issues.** Keep issues where:
- The code will fail to compile or parse (syntax errors, type errors, missing imports)
- The code will definitely produce wrong results (clear logic errors)
- There is a real security vulnerability with evidence
- There is a clear architectural violation of the project's established patterns
- There is a measurable performance problem

Remove:
- Code style or quality concerns
- Potential issues that depend on specific inputs or runtime state
- Subjective suggestions or improvements
- Issues a linter or type checker would catch

### Step 6: Consolidate & Report

Merge all validated findings into a single report. Output to the terminal:

```markdown
# Code Review Report

**Mode**: [diff / commit: <sha> / pr: #<number>]
**Files Reviewed**: [count]
**Issues Found**: [count]
**Status**: APPROVED / NEEDS WORK / BLOCKED

## Critical Issues (must fix)
### 1. [Issue Title]
- **File**: path/to/file.ext:[line]
- **Confidence**: [score]
- **Category**: Security / Architecture / Performance / Bug
- **Issue**: [description]
- **Impact**: [what could go wrong]
- **Fix**: [specific fix suggestion]

## High Priority Issues
[Same format]

## Medium Priority Issues
[Same format]

## Quality Gates
- Critical issues: [count] (0 allowed for APPROVED)
- High issues: [count] (≤2 allowed for APPROVED)
- Medium issues: [count] (≤10 allowed for APPROVED)
- **Result**: PASS / FAIL

## Summary
[2-3 sentences on overall assessment]
```

If no issues were found, output:

```markdown
# Code Review Report

**Mode**: [mode]
**Files Reviewed**: [count]
**Issues Found**: 0
**Status**: APPROVED

No issues found. Reviewed for security vulnerabilities, architecture violations, performance problems, and logic errors.
```

## Quality Gates

| Severity | Threshold | Result |
|----------|-----------|--------|
| Critical | > 0 | BLOCKED |
| High | > 2 | NEEDS WORK |
| Medium | > 10 | NEEDS WORK |
| All within thresholds | — | APPROVED |

## Notes

- This command is **read-only**. It does not modify files, create commits, or push code.
- Only issues with confidence >= 80 are reported.
- All 4 reviewers always run in parallel regardless of file types — each reviewer knows to skip irrelevant files.
- The validation step filters false positives to maintain high signal.

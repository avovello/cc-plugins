---
description: Multi-perspective code review with 19 specialized reviewers covering architecture, security, and performance
---

# Code Review Command

**Purpose**: Multi-perspective code review with 19 specialized reviewers

## Workflow

### Step 1: Analyze Changed Files

Determine file types and which reviewers are needed:

```bash
# Get changed files
git diff --name-only origin/main...HEAD

# Categorize files:
# - Backend: *.php, *.py, *.js (non-frontend), *.go, *.sh
# - Frontend: *.jsx, *.tsx, *.vue, *.html, *.css, *.scss
# - Config: Dockerfile, *.yaml (k8s), .github/workflows/*.yml
```

### Step 2: Launch Parallel Reviewers

**Always Launch** (regardless of file types):
1. **architect-reviewer** - Architecture implications of any change
2. **security-input-reviewer** - Check any user input handling
3. **security-authentication-reviewer** - If auth-related changes detected

**Conditionally Launch** (based on file types changed):

**Security** (3 reviewers):
- **security-authentication-reviewer**: If auth files changed
- **security-input-reviewer**: If files handle user input
- **security-crypto-reviewer**: If crypto/hashing files changed

**Performance** (3 reviewers):
- **performance-algorithm-reviewer**: If algorithm-heavy code changed
- **performance-database-reviewer**: If database query files changed
- **performance-resource-reviewer**: If resource management code changed

**Backend** (5 reviewers - based on file extensions):
- **backend-php-reviewer**: If `*.php` files changed
- **backend-python-reviewer**: If `*.py` files changed
- **backend-nodejs-reviewer**: If `*.js`, `*.ts` (backend) changed
- **backend-go-reviewer**: If `*.go` files changed
- **backend-bash-reviewer**: If `*.sh` files changed

**Frontend** (4 reviewers - based on file extensions):
- **frontend-react-reviewer**: If `*.jsx`, `*.tsx` files changed
- **frontend-vue-reviewer**: If `*.vue` files changed
- **frontend-html-reviewer**: If `*.html` or templates changed
- **frontend-css-reviewer**: If `*.css`, `*.scss`, styled-components changed

**DevOps** (3 reviewers - based on config files):
- **devops-docker-reviewer**: If `Dockerfile`, `docker-compose.yml` changed
- **devops-kubernetes-reviewer**: If k8s `*.yaml` files changed
- **devops-cicd-reviewer**: If `.github/workflows/*.yml` or CI config changed

**Each reviewer must**:
- Review ONLY their domain
- Assign confidence score (0-100) to each issue found
- Filter and return only issues with confidence ≥ 80
- Categorize severity: critical / high / medium / low
- Provide specific file:line citations with GitHub permalinks

### Step 3: Consolidate Review Results

Merge all reviewer outputs:
- Group by severity (critical, high, medium, low)
- Remove duplicate issues (same issue reported by multiple reviewers)
- Sort by confidence score (highest first)

### Step 4: Apply Quality Gates

Check if changes meet quality criteria:
- **Critical issues**: 0 allowed
- **High issues**: ≤ 2 allowed
- **Medium issues**: ≤ 10 allowed

**If quality gates PASS**:
- Proceed to Step 6 (Generate Report)

**If quality gates FAIL**:
- Proceed to Step 5 (Fix Loop)

### Step 5: Fix Loop (max 2 iterations)

**Iteration 1 & 2**:
1. Present critical and high issues to user
2. Apply fixes for critical/high issues
3. Re-run ONLY the affected reviewers (not all 19)
4. Check quality gates again
5. If gates still fail and iteration < 2: repeat
6. If gates still fail and iteration ≥ 2: escalate to user

**Escalation Message**:
```
⚠️ Code review found persistent issues after 2 fix iterations.

Remaining issues:
- Critical: X
- High: Y

Options:
1. Manually address these issues
2. Accept risk and proceed (not recommended for critical issues)
3. Request architectural review
```

### Step 6: Generate Report

Create comprehensive review report:

````markdown
# Code Review Report

**Date**: [ISO date]
**Branch**: [branch name]
**Reviewers**: [list of reviewers that ran]
**Status**: ✅ APPROVED / ⚠️ NEEDS WORK / ❌ BLOCKED

## Summary

- **Files Reviewed**: X
- **Issues Found**: Y
- **Critical**: 0
- **High**: Z
- **Medium**: W
- **Low**: V

## Issues by Severity

### Critical Issues (0)
[None - or list with full details]

### High Priority Issues (Z)

#### 1. [Issue Title]
- **File**: src/auth/login.js:45
- **Reviewer**: security-authentication-reviewer
- **Confidence**: 95
- **Issue**: JWT token not properly validated before use
- **Impact**: Authentication bypass possible
- **Fix**: Add token signature verification before trust
- **Link**: [GitHub permalink]

[Repeat for each high issue]

### Medium Priority Issues (W)
[Brief list with links]

### Low Priority Issues (V)
[Brief list or omit if too many]

## Review by Category

### Architecture Review
- **Reviewer**: architect-reviewer
- **Issues**: X
- **Summary**: [1-2 sentence summary]

### Security Review
- **Reviewers**: 3 security specialists
- **Issues**: X
- **Summary**: [1-2 sentence summary]

[Continue for each category]

## Recommendations

### Must Fix Before Merge
1. [Critical/High issue 1]
2. [Critical/High issue 2]

### Should Fix Soon
1. [Medium issue 1]
2. [Medium issue 2]

### Consider for Future
1. [Low priority improvement 1]
2. [Low priority improvement 2]

## Approval Status

[If APPROVED]:
✅ **APPROVED** - All quality gates passed

[If NEEDS WORK]:
⚠️ **NEEDS WORK** - Address X critical and Y high issues

[If BLOCKED]:
❌ **BLOCKED** - Critical security/architecture issues must be resolved
````

### Step 7: Post Review Actions

- Comment review report on PR (if GitHub PR)
- Save report to `./review-reports/review-[timestamp].md`
- Update review status

## Output Format

The review produces:
- `review-reports/review-[timestamp].md` - Full review report
- Console output with summary
- PR comment (if applicable)

## Usage

```bash
# Review current changes
/review

# Review specific files
/review src/auth/*.js

# Review PR
/review --pr=123
```

## Notes

- Minimum 2 reviewers always run (architect + security-input)
- Maximum 19 reviewers if all file types present
- Average 5-8 reviewers for typical changes
- Review takes 2-5 minutes depending on change size
- All reviewers run in parallel for speed

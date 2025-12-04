# Review Loop Skill

## Purpose

Provides the review-fix-rereview loop capability for automated code quality improvement.

## When to Load

Load this skill when:
- Reviewing code after implementation
- Quality issues need to be fixed
- Implementing the QA-DEV-REVIEW loop

## The Review Loop

```
1. Run Quality Checks
   ↓
2. Manual Code Review
   ↓
3. All Clean? → Done ✅
   ↓ (if issues)
4. Apply Fixes
   ↓
5. Back to Step 1 (max 2 iterations)
```

## Implementation

### Step 1: Automated Quality Checks

```bash
# Linting
npx eslint src/**/*.ts
flake8 src/
phpcs --standard=PSR12 src/

# Formatting
npx prettier --check src/
black --check src/
php-cs-fixer fix --dry-run src/

# Type Checking
npx tsc --noEmit
mypy src/

# Security Scanning
npm audit
bandit -r src/
safety check
```

### Step 2: Manual Review Categories

**Security** (Confidence 90+):
- SQL injection
- XSS vulnerabilities
- Hardcoded secrets
- Missing authentication checks

**Bugs** (Confidence 80+):
- Missing await
- Null dereference
- Race conditions
- Logic errors

**Quality** (Confidence 80+):
- Missing error handling
- Code duplication (>10 lines)
- Missing tests for critical paths

### Step 3: Confidence-Based Filtering

**Only Report 80+ Confidence Issues**:

| Score | Action |
|-------|--------|
| 90-100 | Always report |
| 80-89 | Report |
| Below 80 | Filter out |

### Step 4: Apply Fixes

**Auto-Fix Available**:
```bash
# ESLint auto-fix
npx eslint --fix src/**/*.ts

# Prettier format
npx prettier --write src/

# Python auto-format
black src/
```

**Manual Fixes Required**:
- Logic bugs
- Security vulnerabilities
- Missing error handling

### Step 5: Loop Control

```markdown
## Iteration Tracking

### Iteration 1
- Issues Found: 5
- Fixed: 4
- Remaining: 1

### Iteration 2
- Issues Found: 1
- Fixed: 1
- Remaining: 0

### Result: ✅ All quality checks pass
```

**Max Iterations**: 2 (prevent infinite loops)

## Quality Metrics

```markdown
## Quality Report

### Automated Checks
- Linter: ✅ Pass
- Formatter: ✅ Pass
- Types: ✅ Pass
- Security: ✅ Pass

### Code Metrics
- Average Function Length: 18 lines
- Average Complexity: 4.2
- Max Complexity: 8
- Duplication: 0%

### Security Scan
- SQL Injection: ✅ None
- XSS: ✅ None
- Hardcoded Secrets: ✅ None
```

## Integration with Feature Plugin

This skill is used in Phase 6 (Quality Review) of the feature-dev command:

```markdown
## Phase 6: Quality Review

### Part B: Review Loop
1. Run automated quality checks
2. Launch parallel code-reviewer agents
3. If issues (confidence 80+): Fix → Re-review (max 2 iterations)
4. When all clean: Continue to Summary
```

## Best Practices

1. **Automate First**: Run automated tools before manual review
2. **High Confidence Only**: Don't report uncertain issues
3. **Fix Critical First**: Security → Bugs → Quality
4. **Verify Fixes**: Always re-check after fixing
5. **Document Changes**: Explain why each fix was needed

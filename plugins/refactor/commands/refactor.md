---
description: Systematic technical debt reduction and code refactoring to improve maintainability and reduce complexity
---

# Refactor Command

**Purpose**: Systematic technical debt reduction and code refactoring to improve maintainability, reduce complexity, and modernize code

## Overview

The refactor command addresses the critical gap between identifying technical debt (via Audit plugin) and actually fixing it. Research shows teams spend 10-30% of sprint time on refactoring, making this a critical workflow.

**Key Distinction**: Refactor improves existing code without changing behavior, while Feature adds new functionality and Bugfix repairs broken behavior.

## Workflow

### Step 1: Validate Target

1. **Check target exists**
   - If specific file/directory provided: verify it exists
   - If description provided: identify files needing refactoring
   - If no target: offer to analyze recent Audit plugin output

2. **Determine refactoring scope**
   - Single function refactoring
   - Class/module refactoring
   - Package/directory refactoring
   - Dependency updates
   - Architecture improvement

3. **Identify project type**
   - Language (JavaScript, Python, PHP, Go, Java, etc.)
   - Framework (React, Django, Laravel, etc.)
   - Test framework (Jest, pytest, PHPUnit, etc.)

### Step 2: Analyze Current State

Run **pre-refactor-analyzer** agent to:
- Capture current code structure
- Extract existing tests
- Measure complexity metrics (cyclomatic complexity, nesting depth, function length)
- Document current behavior
- Identify refactoring opportunities
- Estimate impact and effort

**Output**:
```
refactor-plan/
├── CURRENT_STATE.md          # Current code analysis
├── COMPLEXITY_METRICS.md     # Before metrics
├── EXISTING_TESTS.md         # Current test coverage
└── OPPORTUNITIES.md          # Refactoring opportunities
```

### Step 3: Prioritize Improvements

Run **debt-prioritizer** agent to:
- Score each refactoring opportunity by:
  - Impact (how much improvement)
  - Effort (how much work)
  - Risk (how likely to break things)
- Calculate impact/effort ratio
- Recommend priority order
- Identify quick wins (high impact, low effort)
- Flag high-risk refactorings

**Output**: Prioritized list with recommendations

### Step 4: Create Refactoring Plan

Run **refactor-planner** agent to:
- Design refactoring approach
- Break into safe, incremental steps
- Identify test coverage gaps
- Plan test additions/updates
- Define success criteria
- Create rollback plan

**Output**: `REFACTORING_PLAN.md` with step-by-step approach

### Step 5: Review Plan (Optional)

Present plan to user:
- Refactoring steps
- Expected improvements
- Risks and mitigations
- Estimated time

Wait for approval before proceeding.

### Step 6: Launch Parallel Refactoring Agents

Based on refactoring type, launch appropriate agents **in parallel**:

**Always Launch**:
1. **test-preserver** - Ensures tests pass throughout refactoring

**Conditionally Launch** (based on refactoring type):

**Code Structure**:
- **complexity-reducer** - Simplify complex functions/classes
- **duplication-eliminator** - Remove duplicate code
- **naming-improver** - Improve variable/function names

**Modernization**:
- **code-modernizer** - Apply modern language features
- **pattern-upgrader** - Apply better design patterns
- **dependency-updater** - Update outdated dependencies

**Performance**:
- **performance-improver** - Apply performance optimizations identified in analysis

**Architecture**:
- **architecture-improver** - Improve architectural patterns

### Step 7: Apply Refactorings Incrementally

For each refactoring step:

1. **Apply Change**
   - Make single, focused change
   - Keep changes small and atomic

2. **Run Tests** (test-preserver agent)
   - Run relevant test suite
   - Check for failures

3. **If Tests Pass**: ✅
   - Commit change
   - Move to next step

4. **If Tests Fail**: ❌
   - Analyze failure
   - Revert change OR
   - Fix test if behavior change is intentional
   - Re-run tests
   - If still failing after 2 attempts: skip this refactoring, log issue

5. **Measure Progress**
   - Update complexity metrics
   - Track improvements

### Step 8: Verify Improvements

Run **refactor-validator** agent to:
- Re-measure complexity metrics
- Compare before/after
- Verify all tests still pass
- Check no behavior changes
- Validate improvements achieved

**Success Criteria**:
- ✅ All tests pass
- ✅ Complexity reduced (or other goal achieved)
- ✅ No unintended behavior changes
- ✅ Code coverage maintained or improved

### Step 9: Document Changes

Generate documentation:
```
refactor-output/
├── REFACTORING_SUMMARY.md    # What was changed and why
├── IMPROVEMENTS.md            # Before/after metrics
├── COMMIT_MESSAGE.md          # Suggested commit message
└── TECHNICAL_DEBT_UPDATE.md  # Updated tech debt status
```

### Step 10: Create Commit (Optional)

If requested, create git commit:
- Use suggested commit message
- Mark as "Refactor:" in conventional commits format
- Include before/after metrics in commit body

## Refactoring Loop

```
For each refactoring step:
  Apply → Test → (Pass?) → Commit → Next
                    ↓ Fail
                  Revert or Fix → Re-test → (Pass?) → Commit
                                              ↓ Fail (after 2 attempts)
                                            Skip & Log
```

**Max Iterations**: 50 refactoring steps per session
**Safety**: Each step is tested before proceeding

## Configuration

Optional `.claude/refactor-config.yaml`:

```yaml
refactor:
  safety:
    require_test_coverage: true      # Require tests before refactoring
    min_coverage_threshold: 80       # Min coverage % required
    auto_revert_on_failure: true     # Auto revert if tests fail
    max_attempts_per_step: 2         # Max attempts to fix failing tests

  scope:
    max_steps_per_session: 50        # Max refactoring steps
    incremental_commits: true        # Commit after each successful step

  priorities:
    prefer_quick_wins: true          # Prioritize high impact / low effort
    max_risk_level: "medium"         # Skip high-risk refactorings

  metrics:
    track_complexity: true           # Track cyclomatic complexity
    track_duplication: true          # Track code duplication
    track_coverage: true             # Track test coverage
```

## Usage Examples

### Example 1: Reduce Function Complexity

```bash
/refactor "reduce complexity of src/auth/validateUser.js"

# Output:
# ✅ Analyzed validateUser function (complexity: 15)
# ✅ Planned 5 refactoring steps
# ✅ Step 1/5: Extract email validation → tests pass
# ✅ Step 2/5: Extract password validation → tests pass
# ✅ Step 3/5: Extract role checking → tests pass
# ✅ Step 4/5: Simplify conditionals → tests pass
# ✅ Step 5/5: Improve variable names → tests pass
#
# Complexity reduced: 15 → 6 (60% improvement)
# All 23 tests pass ✅
```

### Example 2: Eliminate Code Duplication

```bash
/refactor "eliminate duplication in src/services/"

# Output:
# ✅ Found 8 instances of duplicate code
# ✅ Prioritized by impact (420 lines can be eliminated)
# ✅ Step 1/8: Extract common validation logic → tests pass
# ✅ Step 2/8: Create shared utility function → tests pass
# ...
#
# Duplication eliminated: 420 lines → 65 lines (85% reduction)
# All 156 tests pass ✅
```

### Example 3: Update Dependencies

```bash
/refactor "update outdated dependencies"

# Output:
# ✅ Analyzed 45 dependencies
# ✅ Found 12 outdated (8 with breaking changes)
# ✅ Prioritized by risk
# ✅ Step 1/12: Update lodash 4.17.20 → 4.17.21 → tests pass
# ✅ Step 2/12: Update axios 0.27.0 → 1.6.0 → tests pass
# ⚠️  Step 3/12: Update webpack 4 → 5 → tests fail
#    → Analyzing failures...
#    → Fixed config incompatibility
#    → Re-tested → tests pass
# ...
#
# Updated 12 dependencies
# All 203 tests pass ✅
```

### Example 4: Modernize Code

```bash
/refactor "modernize src/utils/ to ES6+"

# Output:
# ✅ Analyzed code (ES5 patterns detected)
# ✅ Planned modernization steps
# ✅ Step 1/15: Convert var → const/let → tests pass
# ✅ Step 2/15: Convert function() → arrow functions → tests pass
# ✅ Step 3/15: Use destructuring → tests pass
# ✅ Step 4/15: Use template literals → tests pass
# ✅ Step 5/15: Use async/await instead of promises → tests pass
# ...
#
# Modernized 45 files to ES6+
# All 178 tests pass ✅
```

### Example 5: Address Audit Findings

```bash
# After running /audit, you have TECHNICAL_DEBT.md with issues
/refactor "address high-priority items from audit"

# Output:
# ✅ Loaded audit-output/TECHNICAL_DEBT.md
# ✅ Found 5 high-priority items
# ✅ Prioritized by impact/effort
#
# Item 1/5: God class UserService (650 lines)
#   ✅ Split into UserAuthService
#   ✅ Split into UserProfileService
#   ✅ Split into UserNotificationService
#   → tests pass
#
# Item 2/5: Long function processPayment (120 lines)
#   ✅ Extract validation logic
#   ✅ Extract payment processing
#   ✅ Extract notification logic
#   → tests pass
# ...
```

## Quality Gates

Before completing refactoring:

- ✅ All tests pass
- ✅ Code coverage maintained or improved
- ✅ Complexity reduced (if that was the goal)
- ✅ No unintended behavior changes
- ✅ No new linter errors

If any gate fails:
- Report issue
- Offer to revert changes
- Suggest next steps

## Safety Features

1. **Test-Driven**: Every step is tested before proceeding
2. **Incremental**: Small, atomic changes that can be reverted
3. **Automatic Rollback**: Failed changes are reverted automatically
4. **Behavior Preservation**: Verifies no unintended behavior changes
5. **Risk Assessment**: High-risk refactorings flagged for review

## Output Files

```
refactor-output/
├── CURRENT_STATE.md           # Initial analysis
├── REFACTORING_PLAN.md        # Step-by-step plan
├── REFACTORING_SUMMARY.md     # What changed
├── IMPROVEMENTS.md            # Before/after metrics
├── COMPLEXITY_BEFORE.md       # Initial metrics
├── COMPLEXITY_AFTER.md        # Final metrics
├── COMMIT_MESSAGE.md          # Suggested commit message
└── TECHNICAL_DEBT_UPDATE.md   # Updated debt status
```

## Integration with Other Plugins

- **After Audit**: `/audit` identifies debt → `/refactor` fixes it
- **Before Feature**: Refactor first to create clean foundation
- **With Review**: Can refactor based on review feedback
- **With Optimize**: Refactor improves structure, Optimize improves speed

## Notes

- **Behavior Preservation**: Refactoring should NOT change behavior
- **Test Coverage**: Requires existing tests (or creates them first)
- **Incremental**: Small steps are safer than big rewrites
- **Continuous**: Refactoring should be ongoing, not a one-time event
- **Team Practice**: Research shows teams spend 10-30% of time on this

## Best Practices

1. **Refactor in small steps** - Easier to verify and revert
2. **Test after each step** - Catch issues immediately
3. **Commit frequently** - Each successful step can be committed
4. **Focus on one thing** - Don't mix refactoring types
5. **Measure improvements** - Track metrics to validate benefits
6. **Use for onboarding** - Refactoring is a great way to learn code

## Common Refactoring Patterns

The plugin supports these common refactorings:

**Method-Level**:
- Extract Method
- Inline Method
- Rename Method
- Add Parameter
- Remove Parameter

**Class-Level**:
- Extract Class
- Inline Class
- Extract Interface
- Move Method
- Move Field

**Code Cleanup**:
- Remove Duplication
- Simplify Conditionals
- Replace Magic Numbers
- Improve Naming

**Modernization**:
- Use Modern Syntax
- Update Dependencies
- Apply Design Patterns
- Improve Error Handling

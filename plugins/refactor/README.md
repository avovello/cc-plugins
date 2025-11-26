# Refactor Plugin 🔧

Systematic technical debt reduction and code refactoring with continuous improvement cycles.

## Overview

The Refactor plugin addresses the **critical gap** between identifying technical debt (via Audit plugin) and actually fixing it. Research shows teams spend **10-30% of sprint time** on refactoring, making this workflow essential.

**Key Distinction**:
- **Refactor** improves existing code without changing behavior
- **Feature** adds new functionality
- **Bugfix** repairs broken behavior

## Problem It Solves

```
Current Gap:
  Audit Plugin: "You have 47 tech debt issues" ❌ NO WORKFLOW TO FIX THEM
         ↓
  [MISSING PLUGIN]
         ↓
  Manual refactoring (risky, time-consuming)
```

```
With Refactor Plugin:
  Audit Plugin: "You have 47 tech debt issues"
         ↓
  Refactor Plugin: Systematic, tested, incremental fixes ✅
         ↓
  Clean, maintainable codebase
```

## Use Cases

- **Reduce Complexity**: Simplify overly complex functions (complexity > 10)
- **Eliminate Duplication**: Extract repeated code into shared functions
- **Modernize Code**: Update to modern language features (ES6+, Python 3, PHP 8)
- **Update Dependencies**: Safely update outdated packages
- **Address Audit Findings**: Fix high-priority technical debt from audit reports
- **Improve Architecture**: Apply better design patterns
- **Before Features**: Clean foundation before adding new functionality

## Usage

```bash
/refactor "reduce complexity in src/auth/validateUser.js"
/refactor "eliminate duplication in src/services/"
/refactor "update outdated dependencies"
/refactor "modernize src/utils/ to ES6+"
/refactor "address high-priority items from audit"
/refactor                                    # Interactive mode
```

## Workflow

### 1. **Analyze** Current State
- Measure complexity, duplication, coverage
- Identify refactoring opportunities
- Document current behavior

### 2. **Prioritize** Improvements
- Score by impact/effort/risk
- Identify "quick wins" (high impact, low effort)
- Create prioritized roadmap

### 3. **Plan** Refactoring
- Design incremental steps
- Identify test coverage gaps
- Define success criteria

### 4. **Refactor** Incrementally
```
For each step:
  Apply Change → Run Tests → Pass? → Commit → Next
                              ↓ Fail
                          Revert or Fix → Re-test
```

### 5. **Validate** Improvements
- Verify all tests pass
- Compare before/after metrics
- Confirm goal achieved

## Specialized Agents (7)

### 1. **debt-prioritizer**
**Focus**: Prioritize technical debt by impact/effort/risk

**Creates**:
- Prioritized refactoring roadmap
- Quick wins identification (high impact, low effort)
- Risk assessment for each item

### 2. **complexity-reducer**
**Focus**: Reduce cyclomatic complexity

**Techniques**:
- Extract Method
- Guard Clauses (early returns)
- Decompose Conditional
- Replace Nested Loops with Pipelines

### 3. **test-preserver**
**Focus**: Ensure tests pass throughout refactoring

**Monitors**:
- Test execution after each change
- Code coverage (maintain or improve)
- Behavior preservation

### 4. **duplication-eliminator**
**Focus**: Remove code duplication

**Techniques**:
- Extract shared functions
- Parameterize variations
- Apply strategy patterns

### 5. **code-modernizer**
**Focus**: Apply modern language features

**Updates**:
- ES5 → ES6+ (var→const/let, promises→async/await)
- Python 2 → Python 3 (f-strings, type hints)
- PHP 5 → PHP 8 (arrow functions, type declarations)

### 6. **dependency-updater**
**Focus**: Update outdated dependencies

**Handles**:
- Incremental updates (one at a time)
- Breaking change analysis
- Test validation after each update

### 7. **refactor-validator**
**Focus**: Validate refactoring success

**Verifies**:
- All tests pass
- Goal achieved
- Metrics improved
- No behavior changes

## Examples

### Example 1: Reduce Function Complexity

```bash
/refactor "reduce complexity of src/auth/validateUser.js"
```

**Output**:
```
✅ Analyzed validateUser function (complexity: 15)
✅ Planned 5 refactoring steps
✅ Step 1/5: Extract email validation → tests pass
✅ Step 2/5: Extract password validation → tests pass
✅ Step 3/5: Extract role checking → tests pass
✅ Step 4/5: Simplify conditionals → tests pass
✅ Step 5/5: Improve variable names → tests pass

Complexity reduced: 15 → 6 (60% improvement)
All 23 tests pass ✅
```

### Example 2: Eliminate Duplication

```bash
/refactor "eliminate duplication in src/services/"
```

**Output**:
```
✅ Found 8 instances of duplicate code
✅ Prioritized by impact (420 lines can be eliminated)
✅ Step 1/8: Extract common validation logic → tests pass
✅ Step 2/8: Create shared utility function → tests pass
...

Duplication eliminated: 420 lines → 65 lines (85% reduction)
All 156 tests pass ✅
```

### Example 3: Address Audit Findings

```bash
# After running /audit
/refactor "address high-priority items from audit"
```

**Output**:
```
✅ Loaded audit-output/TECHNICAL_DEBT.md
✅ Found 5 high-priority items

Item 1/5: God class UserService (650 lines)
  ✅ Split into UserAuthService
  ✅ Split into UserProfileService
  ✅ Split into UserNotificationService
  → tests pass

Item 2/5: Long function processPayment (120 lines)
  ✅ Extract validation logic
  ✅ Extract payment processing
  ✅ Extract notification logic
  → tests pass
...

Technical debt reduced: 5 high-priority items resolved
All 203 tests pass ✅
```

## Safety Features

1. **Test-Driven**: Every step is tested before proceeding
2. **Incremental**: Small, atomic changes that can be reverted
3. **Automatic Rollback**: Failed changes are reverted automatically
4. **Behavior Preservation**: Verifies no unintended behavior changes
5. **Risk Assessment**: High-risk refactorings flagged for review

## Output

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

## Configuration

Optional `.claude/refactor-config.yaml`:

```yaml
refactor:
  safety:
    require_test_coverage: true      # Require tests before refactoring
    min_coverage_threshold: 80       # Min coverage % required
    auto_revert_on_failure: true     # Auto revert if tests fail

  scope:
    max_steps_per_session: 50        # Max refactoring steps

  priorities:
    prefer_quick_wins: true          # Prioritize high impact / low effort
    max_risk_level: "medium"         # Skip high-risk refactorings

  metrics:
    track_complexity: true           # Track cyclomatic complexity
    track_duplication: true          # Track code duplication
    track_coverage: true             # Track test coverage
```

## Related Plugins

- **QA Plugin**: After refactoring, run comprehensive tests (`/qa`, `/e2e`, `/unit`, `/integration`)
- **After Audit**: `/audit` identifies debt → `/refactor` fixes it
- **Before Feature**: Refactor first to create clean foundation

## Integration with Other Plugins

- **After Audit**: `/audit` identifies debt → `/refactor` fixes it
- **Before Feature**: Refactor first to create clean foundation
- **With Review**: Can refactor based on review feedback
- **With Optimize**: Refactor improves structure, Optimize improves speed

## Quality Gates

Before completing refactoring:

- ✅ All tests pass
- ✅ Code coverage maintained or improved
- ✅ Complexity reduced (if that was the goal)
- ✅ No unintended behavior changes
- ✅ No new linter errors

## Best Practices

1. **Refactor in small steps** - Easier to verify and revert
2. **Test after each step** - Catch issues immediately
3. **Commit frequently** - Each successful step can be committed
4. **Focus on one thing** - Don't mix refactoring types
5. **Measure improvements** - Track metrics to validate benefits

## Statistics

- **Agents**: 7 specialized refactoring agents
- **Refactoring Patterns**: 20+ supported patterns
- **Safety**: 100% test coverage required
- **Industry Standard**: Teams spend 10-30% of time on this

## Why This Plugin Exists

Research shows:
- Teams spend **10-30% of sprint time** refactoring
- Technical debt slows development by **25-50%**
- **Boy Scout Rule**: Leave code better than you found it
- **Continuous refactoring** > big rewrites

Yet most teams lack a systematic, safe workflow for refactoring. This plugin provides that workflow.

## Notes

- **Behavior Preservation**: Refactoring should NOT change behavior
- **Test Coverage**: Requires existing tests (or creates them first)
- **Incremental**: Small steps are safer than big rewrites
- **Continuous**: Refactoring should be ongoing, not a one-time event

---

**Priority**: P0 - CRITICAL
**Why**: Closes the biggest gap in our marketplace (identification vs action)

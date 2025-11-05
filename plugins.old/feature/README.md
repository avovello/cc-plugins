# Feature Plugin ✨

Complete feature development lifecycle with comprehensive quality loops.

## Overview

The Feature Plugin implements a professional development workflow that includes planning, implementation, testing loops, code review loops, documentation, and deployment preparation.

## Workflow Diagram

```
┌─────────────┐
│   Planning  │
└──────┬──────┘
       │
       ▼
┌─────────────────┐
│ Planning Review │◄─────┐
└──────┬──────────┘      │
       │ approved?        │
       │ no───────────────┘
       │ yes
       ▼
┌──────────────────┐
│ Implementation   │
└──────┬───────────┘
       │
       ▼
┌──────────────┐
│   Testing    │◄─────────────┐
└──────┬───────┘              │
       │ passed?               │
       │ no                    │
       ├──────┐                │
       │      ▼                │
       │  ┌─────────────┐     │
       │  │ Bug Analysis│     │
       │  └──────┬──────┘     │
       │         ▼             │
       │  ┌─────────┐         │
       │  │   Fix   │─────────┘
       │  └─────────┘
       │ yes
       ▼
┌──────────────┐
│ Code Review  │◄──────────┐
└──────┬───────┘           │
       │ approved?          │
       │ no                 │
       ├──────┐             │
       │      ▼             │
       │  ┌────────────┐   │
       │  │Review Fixes│───┘
       │  └────────────┘
       │ yes
       ▼
┌────────────────┐
│ Documentation  │
└────────┬───────┘
         ▼
┌────────────────┐
│ Deploy Prep    │
└────────────────┘
```

## Processing Loops

### 1. Planning Loop
- **Planning** → **Review** → (if not approved) **Revise** → **Review**
- Ensures plan is solid before implementation begins
- Maximum 2 iterations

### 2. Testing Loop
- **Test** → (if failures) **Analyze** → **Fix** → **Re-test**
- Repeats until all tests pass
- Maximum 3 iterations
- Escalates if tests still fail after max iterations

### 3. Review Loop
- **Code Review** → (if changes requested) **Apply Feedback** → **Re-review**
- Ensures code quality before merge
- Maximum 2 iterations

## Phases

### Phase 1: Planning (with Review Loop)
1. **Planning**
   - Analyze feature request
   - Design architecture changes
   - Identify files to modify
   - Create test strategy
   - Assess risks

2. **Planning Review**
   - Review plan completeness
   - Check architectural soundness
   - Identify missing considerations
   - Approve or request revisions

3. **Planning Revision** (if needed)
   - Address review feedback
   - Revise plan
   - Re-submit for review

### Phase 2: Implementation
- Implement the approved plan
- Follow coding standards
- Create necessary files
- Make required changes

### Phase 3: Testing (with Bug Fix Loop)
1. **Test Execution**
   - Run full test suite
   - Capture results
   - Identify failures

2. **Bug Analysis** (if failures)
   - Analyze each failure
   - Identify root causes
   - Plan fixes

3. **Fix Implementation** (if failures)
   - Apply fixes
   - Return to testing

4. **Repeat** until tests pass (max 3 times)

### Phase 4: Code Review (with Feedback Loop)
1. **Code Review**
   - Review all changes
   - Check code quality
   - Verify best practices
   - Security review
   - Performance review

2. **Apply Feedback** (if changes requested)
   - Address review comments
   - Make requested changes
   - Re-submit for review

3. **Repeat** until approved (max 2 times)

### Phase 5: Documentation
- Update/create documentation
- Add code comments
- Update CHANGELOG
- Create migration guide if needed

### Phase 6: Deployment Preparation
- Create deployment checklist
- Document deployment steps
- Create rollback plan
- List post-deployment validation steps

## Subagents

- **Planner**: Creates comprehensive feature plans
- **Implementer**: Implements features following plans
- **Tester**: Executes and analyzes tests
- **Bug Analyzer**: Analyzes failures and recommends fixes
- **Code Reviewer**: Performs comprehensive code reviews
- **Documenter**: Creates and updates documentation

## Configuration

```yaml
# .claude/feature-config.yaml
feature:
  planning:
    max_review_iterations: 2
    require_architecture_review: true

  testing:
    max_fix_iterations: 3
    require_full_suite: true
    min_coverage: 80

  review:
    max_iterations: 2
    require_security_review: true
    require_performance_review: false

  documentation:
    update_changelog: true
    require_migration_guide_if_breaking: true
```

## Usage

### Via Slash Command
```bash
/feature "Add user authentication with OAuth2"
```

### Via Workflow
```bash
claude-code workflow feature \
  --description="Add user authentication" \
  --max-test-retries=3 \
  --max-review-iterations=2
```

## Hooks

### Available Hooks
- `pre-planning`: Runs before planning begins
- `post-planning-review`: Runs after planning review
- `pre-implementation`: Runs before implementation
- `post-implementation`: Runs after implementation
- `pre-test`: Runs before tests
- `on-test-failure`: Runs when tests fail
- `post-test-success`: Runs when all tests pass
- `pre-review`: Runs before code review
- `on-review-changes-requested`: Runs when reviewer requests changes
- `post-review-approved`: Runs when review is approved
- `pre-deploy`: Runs before deployment preparation

## Success Criteria

Feature is complete when:
- [x] Plan created and reviewed
- [x] Implementation complete
- [x] All tests passing
- [x] Code review approved
- [x] Documentation updated
- [x] Deployment plan ready

## Examples

See `examples/feature/` for detailed examples:
- Simple feature addition
- Complex feature with breaking changes
- Feature requiring database migration
- Feature with external API integration

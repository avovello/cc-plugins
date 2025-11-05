# Feature Development Workflow

This workflow implements the complete feature development lifecycle with comprehensive quality loops.

## Input Parameters

```yaml
feature_description: "Description of the feature to implement"
max_test_iterations: 3
max_review_iterations: 2
require_full_test_suite: true
update_documentation: true
```

---

## Workflow State

You maintain state throughout this workflow:

```yaml
state:
  phase: "planning | implementation | testing | review | documentation | deploy"
  plan: null
  plan_approved: false
  test_iteration: 0
  all_tests_passed: false
  review_iteration: 0
  review_approved: false
  changes_made: []
```

---

## Phase 1: Planning with Review Loop

### Step 1.1: Create Plan

**Use TodoWrite** to track: "Create feature implementation plan"

Analyze the feature request and create a comprehensive plan:

1. **Feature Analysis**
   - What is being requested?
   - What problem does it solve?
   - What are the acceptance criteria?

2. **Architecture Design**
   - What components need to be created/modified?
   - How does this fit into existing architecture?
   - What design patterns should be used?

3. **Implementation Plan**
   - Files to create: [list]
   - Files to modify: [list]
   - New dependencies: [list]
   - Database changes: [list if applicable]

4. **Testing Strategy**
   - Unit tests needed
   - Integration tests needed
   - Edge cases to cover
   - Test data requirements

5. **Risk Assessment**
   - Technical risks
   - Breaking changes
   - Performance concerns
   - Security considerations

6. **Timeline Estimate**
   - Development time
   - Testing time
   - Review time

**Output**: `state.plan` = comprehensive plan

Mark todo complete, add "Review implementation plan"

---

### Step 1.2: Review Plan

**Use TodoWrite** to track: "Review implementation plan"

Review the plan critically:

1. **Completeness Check**
   - Are all requirements addressed?
   - Are edge cases considered?
   - Is the test strategy comprehensive?

2. **Architecture Review**
   - Does it follow existing patterns?
   - Are there simpler approaches?
   - Will it scale?
   - Are there security issues?

3. **Risk Review**
   - Are risks properly identified?
   - Are there mitigation strategies?
   - Are breaking changes documented?

**Decision**: Approve or request revisions

**Output**:
- `state.plan_approved` = true/false
- `review_feedback` = list of issues if not approved

---

### Step 1.3: Revise Plan (if not approved)

**Condition**: `!state.plan_approved && planning_iteration < 2`

Address review feedback and revise the plan.

**Output**: Updated `state.plan`

**Action**: Return to Step 1.2 (Review Plan)

---

## Phase 2: Implementation

### Step 2.1: Implement Feature

**Use TodoWrite** to track: "Implement feature according to plan"

Mark planning complete, add implementation todo.

Follow the approved plan to implement the feature:

1. **Setup**
   - Create necessary directories
   - Install dependencies if needed

2. **Implementation**
   - Create new files as planned
   - Modify existing files as planned
   - Follow coding standards
   - Add appropriate comments

3. **Verification**
   - Verify syntax
   - Basic functionality check

**Output**: `state.changes_made` = list of files changed

Mark implementation complete, add "Run test suite"

---

## Phase 3: Testing Loop

### Step 3.1: Run Tests

**Use TodoWrite** to track: "Run test suite"

Invoke the **Tester subagent** (use `subagents/tester.md`):

1. Detect test framework
2. Run test suite
3. Capture results
4. Parse failures

**Output**:
- `test_results` = detailed test results
- `state.all_tests_passed` = boolean
- `failed_tests` = array of failures

---

### Step 3.2: Check Test Results

**If** `state.all_tests_passed`:
- Mark testing complete
- **Proceed to Phase 4: Code Review**

**If** `!state.all_tests_passed && state.test_iteration >= max_test_iterations`:
- Mark testing as "Tests still failing after max retries"
- **Escalate**: Create summary of persistent failures
- **Ask user** for guidance
- **Stop workflow**

**If** `!state.all_tests_passed && state.test_iteration < max_test_iterations`:
- **Proceed to Step 3.3: Analyze Bugs**

---

### Step 3.3: Analyze Test Failures

**Use TodoWrite** to track: "Analyze test failures"

Invoke the **Bug Analyzer subagent** (use `subagents/bug-analyzer.md`):

Input: `test_results`, `failed_tests`

The bug analyzer will:
1. Analyze each failure
2. Identify root causes
3. Categorize failures
4. Recommend fixes

**Output**:
- `bug_analysis` = detailed analysis
- `fix_recommendations` = specific fixes to apply

Mark analysis complete, add "Apply bug fixes"

---

### Step 3.4: Apply Fixes

**Use TodoWrite** to track: "Apply bug fixes"

Based on `fix_recommendations`, fix each issue:

1. Read the relevant files
2. Make the necessary changes
3. Verify the fix makes sense

**Output**: Updated `state.changes_made`

Increment `state.test_iteration`

Mark fixes complete

**Action**: Return to Step 3.1 (Run Tests)

---

## Phase 4: Code Review Loop

### Step 4.1: Perform Code Review

**Use TodoWrite** to track: "Perform code review"

Review all changes made (`state.changes_made`):

1. **Code Quality Review**
   - Is the code clean and readable?
   - Are variable names clear?
   - Is there proper error handling?
   - Are edge cases handled?

2. **Best Practices Review**
   - Follows language conventions?
   - Uses appropriate design patterns?
   - No code duplication?
   - Proper separation of concerns?

3. **Security Review**
   - Input validation present?
   - No security vulnerabilities?
   - Secrets not hardcoded?
   - Proper authentication/authorization?

4. **Performance Review**
   - No obvious performance issues?
   - Efficient algorithms used?
   - No memory leaks?

**Output**:
- `state.review_approved` = true/false
- `review_comments` = array of issues if not approved

---

### Step 4.2: Check Review Status

**If** `state.review_approved`:
- Mark review complete
- **Proceed to Phase 5: Documentation**

**If** `!state.review_approved && state.review_iteration >= max_review_iterations`:
- Mark as "Review not approved after max iterations"
- **Create summary** of remaining issues
- **Ask user** for guidance
- **Stop workflow**

**If** `!state.review_approved && state.review_iteration < max_review_iterations`:
- **Proceed to Step 4.3: Apply Review Feedback**

---

### Step 4.3: Apply Review Feedback

**Use TodoWrite** to track: "Address code review comments"

Address each review comment in `review_comments`:

1. Read the specific feedback
2. Make the requested changes
3. Verify the change addresses the feedback

**Output**: Updated `state.changes_made`

Increment `state.review_iteration`

Mark feedback application complete

**Action**: Return to Step 4.1 (Perform Code Review)

---

## Phase 5: Documentation

### Step 5.1: Create/Update Documentation

**Use TodoWrite** to track: "Create/update documentation"

Invoke the **Documenter subagent** (use `subagents/documenter.md`):

Create/update:
1. **Code Comments**
   - Add JSDoc/docstrings to new functions
   - Explain complex logic

2. **README Updates**
   - Document new feature
   - Add usage examples
   - Update API documentation

3. **CHANGELOG**
   - Add entry for new feature
   - Note any breaking changes

4. **Migration Guide** (if breaking changes)
   - Document what changed
   - Provide migration steps
   - Show before/after examples

**Output**: List of documentation files created/updated

Mark documentation complete

---

## Phase 6: Deployment Preparation

### Step 6.1: Create Deployment Plan

**Use TodoWrite** to track: "Create deployment plan"

Create a deployment checklist:

1. **Pre-Deployment Checklist**
   - [ ] All tests passing
   - [ ] Code review approved
   - [ ] Documentation updated
   - [ ] Database migrations prepared (if applicable)
   - [ ] Environment variables documented
   - [ ] Dependencies updated in production

2. **Deployment Steps**
   - Step-by-step deployment instructions
   - Commands to run
   - Expected output

3. **Rollback Plan**
   - How to rollback if issues occur
   - Data rollback if applicable
   - Quick rollback commands

4. **Post-Deployment Validation**
   - [ ] Health check endpoints
   - [ ] Feature verification steps
   - [ ] Monitoring checks
   - [ ] User acceptance testing

**Output**: `DEPLOYMENT_PLAN.md` file

Mark deployment plan complete

---

## Workflow Complete

Present final summary:

```markdown
# Feature Development Complete ✅

## Feature
{{feature_description}}

## Summary
- Planning iterations: {{planning_iterations}}
- Test iterations: {{state.test_iteration}}
- Review iterations: {{state.review_iteration}}

## Changes Made
{{state.changes_made}}

## Documentation Updated
{{documentation_files}}

## Next Steps
1. Review DEPLOYMENT_PLAN.md
2. Execute deployment when ready
3. Monitor post-deployment

## Deliverables
- [x] Feature implemented
- [x] Tests passing
- [x] Code reviewed
- [x] Documentation updated
- [x] Deployment plan created
```

---

## Error Handling

### Test Failures Persist
If tests still fail after `max_test_iterations`:
1. Create detailed failure summary
2. List all attempted fixes
3. Recommend next steps (debugging, design change, etc.)
4. Ask user for input

### Review Not Approved
If review not approved after `max_review_iterations`:
1. Create summary of remaining issues
2. Explain what has been addressed
3. Ask user for prioritization of remaining items

### Unexpected Errors
- Capture error details
- Save current state
- Provide clear error message
- Ask user how to proceed

---

## Best Practices

1. **Use TodoWrite extensively** - Keep user informed of progress
2. **Be thorough in planning** - Good plan = smoother implementation
3. **Don't skip reviews** - Quality gates prevent issues downstream
4. **Document as you go** - Easier than documenting after the fact
5. **Learn from failures** - Each test failure teaches something
6. **Communicate clearly** - Keep user informed at each phase

---

## Customization

This workflow can be customized by:
- Adjusting `max_test_iterations` and `max_review_iterations`
- Adding custom hooks at key points
- Modifying review criteria
- Adding additional phases (e.g., performance testing)
- Integrating with external tools (CI/CD, issue trackers)

# Claude Code Marketplace - Usage Guide

This guide shows you how to use each plugin effectively.

## Table of Contents

1. [Quick Start](#quick-start)
2. [Investigation Plugin Examples](#investigation-plugin)
3. [Research Plugin Examples](#research-plugin)
4. [Review Plugin Examples](#review-plugin)
5. [Feature Plugin Examples](#feature-plugin)
6. [Bugfix Plugin Examples](#bugfix-plugin)
7. [Custom Workflows](#custom-workflows)
8. [Hooks and Automation](#hooks-and-automation)

---

## Quick Start

### Installation

1. Clone the marketplace repository:
```bash
git clone https://github.com/avovello/claude-code-marketplace.git
cd claude-code-marketplace
```

2. Link plugins to your Claude Code workspace:
```bash
# In your project directory
mkdir -p .claude/workflows
mkdir -p .claude/commands

# Link desired plugins
ln -s /path/to/claude-code-marketplace/plugins/feature .claude/workflows/feature
ln -s /path/to/claude-code-marketplace/plugins/bugfix .claude/workflows/bugfix
```

3. Create configuration (optional):
```bash
cp claude-code-marketplace/examples/config.yaml .claude/marketplace-config.yaml
```

---

## Investigation Plugin

### Example 1: Investigate Entire Codebase

**Scenario**: You've joined a new project and need to understand the codebase.

**Request**:
```
I need to understand this codebase. Can you investigate the architecture,
identify key components, and create onboarding documentation?
```

**What Happens**:
1. Explorer maps directory structure
2. Identifies entry points and dependencies
3. Analyzer examines architecture patterns
4. Documents component relationships
5. Creates onboarding guide

**Output**:
- `ARCHITECTURE.md` - System architecture overview
- `COMPONENTS.md` - Component documentation
- `DEPENDENCIES.md` - Dependency graph
- `ONBOARDING.md` - New developer guide

---

### Example 2: Focused Investigation

**Scenario**: You need to understand just the authentication system.

**Request**:
```
Investigate the authentication system in src/auth/ directory.
I need to understand how it works and identify any security concerns.
```

**What Happens**:
1. Focuses on `src/auth/` directory
2. Maps authentication flow
3. Identifies security patterns
4. Documents findings

---

## Research Plugin

### Example 1: Technology Comparison

**Scenario**: Choosing between state management libraries.

**Request**:
```
Research and compare Redux, MobX, and Zustand for our React application.
Our app has moderate complexity with real-time updates.
```

**What Happens**:
1. Researches each library
   - Redux: Comprehensive research on features, patterns, ecosystem
   - MobX: Research on reactivity, simplicity, performance
   - Zustand: Research on minimalism, TypeScript support

2. Creates comparison matrix:
```markdown
| Criteria         | Redux | MobX | Zustand |
|------------------|-------|------|---------|
| Learning Curve   | High  | Med  | Low     |
| Boilerplate      | High  | Low  | Low     |
| DevTools         | ✅    | ✅   | ✅      |
| Real-time        | Good  | Exc  | Good    |
| TypeScript       | Exc   | Good | Exc     |
```

3. Evaluates for your use case
4. Recommends: "Zustand for simplicity, Redux if ecosystem matters"

**Output**: `RESEARCH_REPORT.md` with detailed comparison

---

### Example 2: API Exploration

**Scenario**: Need to integrate with Stripe API.

**Request**:
```
Research Stripe API for implementing subscription billing.
Focus on webhooks and customer portal.
```

**What Happens**:
1. Explores Stripe API documentation
2. Identifies key endpoints (create subscription, webhooks, portal)
3. Documents webhook events
4. Provides implementation guidance

**Output**:
- API reference guide
- Webhook integration checklist
- Code examples

---

## Review Plugin

### Example 1: Pre-Merge Code Review

**Scenario**: You've implemented a feature and want comprehensive review.

**Request**:
```
Review all my changes with focus on security and performance.
```

**What Happens**:
1. **Code Quality Review**
   - Checks readability, maintainability
   - Identifies code smells
   - Suggests improvements

2. **Security Review**
   - Checks for SQL injection vulnerabilities
   - Validates input sanitization
   - Reviews authentication logic
   - Checks for hardcoded secrets

3. **Performance Review**
   - Identifies inefficient queries
   - Checks for memory leaks
   - Reviews algorithm complexity

4. **Consolidates Feedback**
   - Priority: Critical security issue in login.js:45
   - Priority: High performance concern in users.js:120
   - Priority: Medium refactoring suggestion in utils.js

5. **Review Loop** (if issues found)
   - You fix issues
   - Re-review
   - Approved when all critical/high issues resolved

**Output**: `REVIEW_REPORT.md` with actionable feedback

---

### Example 2: Architecture Review

**Scenario**: Major refactoring needs architectural review.

**Request**:
```
Review the architectural changes in my refactoring branch.
Focus on design patterns and maintainability.
```

**What Happens**:
- Reviews architectural decisions
- Checks SOLID principles
- Evaluates separation of concerns
- Provides architectural feedback

---

## Feature Plugin

### Example 1: Simple Feature

**Scenario**: Add email validation to registration form.

**Request**:
```
Add email validation to the user registration form.
Emails should be validated both client-side and server-side.
```

**What The Workflow Does**:

**1. Planning Phase**
```markdown
Plan created:
- Client-side: Add email validation in RegisterForm.jsx
- Server-side: Add validation in user.controller.js
- Add email validation utility function
- Test cases: valid emails, invalid emails, edge cases
- Risk: None (additive change)
```

**2. Planning Review**
```markdown
Review feedback:
✅ Plan is comprehensive
✅ Both client and server validation covered
✅ Test cases identified
Approved: Yes
```

**3. Implementation**
- Creates `utils/emailValidator.js`
- Updates `RegisterForm.jsx` with validation
- Updates `user.controller.js` with server validation

**4. Testing Loop**
```
Test Run 1:
- ❌ Email validation test failing
- Bug Analysis: Regex doesn't handle '+' in email
- Fix Applied: Updated regex pattern
- Test Run 2:
- ✅ All tests passing
```

**5. Code Review**
```markdown
Review:
✅ Code quality: Good
✅ Security: Input sanitization present
✅ Performance: No concerns
💡 Suggestion: Extract regex to constant
Approved: Yes (after applying suggestion)
```

**6. Documentation**
- Updated `README.md` with email requirements
- Added JSDoc to emailValidator
- Updated CHANGELOG

**7. Deploy Prep**
- Created deployment checklist
- No database changes needed
- No environment variables needed

---

### Example 2: Complex Feature with Loops

**Scenario**: Add real-time notifications system.

**Request**:
```
Implement a real-time notification system using WebSockets.
Users should receive notifications for mentions and messages.
```

**What The Workflow Does**:

**1. Planning Phase**
```markdown
Plan created:
- Backend: WebSocket server with Socket.IO
- Database: Notifications table
- API: CRUD endpoints for notifications
- Frontend: NotificationProvider context
- Frontend: NotificationBell component
- Testing: WebSocket testing, notification delivery
- Risk: WebSocket connections at scale
```

**2. Planning Review - Iteration 1**
```markdown
Review feedback:
❌ No consideration for notification persistence
❌ Missing authentication for WebSocket connections
❌ No strategy for offline users
Approved: No

Revisions:
- Added Redis for notification persistence
- WebSocket auth middleware
- Queue system for offline users

Review - Iteration 2:
✅ All concerns addressed
Approved: Yes
```

**3. Implementation**
- Implements WebSocket server
- Creates database migrations
- Builds frontend components

**4. Testing Loop - Multiple Iterations**
```
Test Run 1:
- ❌ 5 tests failing
- Bug Analysis: WebSocket auth not working
- Fix: Corrected JWT verification
- Test Run 2:
- ❌ 2 tests still failing
- Bug Analysis: Redis connection timing issue
- Fix: Added connection retry logic
- Test Run 3:
- ✅ All tests passing
```

**5. Code Review Loop**
```
Review 1:
❌ Security: WebSocket messages not validated
❌ Performance: No rate limiting on notifications
Approved: No

Fixes Applied:
- Added message validation
- Implemented rate limiting

Review 2:
✅ All issues addressed
Approved: Yes
```

**6. Documentation**
- Extensive README update
- WebSocket API documentation
- Deployment guide with Redis setup
- Migration guide

**Total Iterations**:
- Planning reviews: 2
- Test-fix cycles: 3
- Code review cycles: 2

---

## Bugfix Plugin

### Example 1: Simple Bug

**Scenario**: Login button not working.

**Request**:
```
Fix the bug where clicking the login button doesn't submit the form.
```

**What The Workflow Does**:

**1. Reproduction**
```markdown
Reproduction Steps:
1. Navigate to /login
2. Enter credentials
3. Click "Login" button
4. Expected: Form submits
5. Actual: Nothing happens

Reproduced: ✅

Test Created:
test('login button submits form', () => {
  // Test that currently fails
});
```

**2. Root Cause Analysis**
```markdown
Analysis:
- Examined LoginForm.jsx
- Button has type="button" instead of type="submit"
- Event handler is attached but preventDefault called without submit

Root Cause: Button type is "button", should be "submit"
Category: Logic error
Severity: Medium
```

**3. Fix Planning**
```markdown
Fix Plan:
- Change button type from "button" to "submit"
- Remove redundant onClick handler
- Simplest fix, no side effects expected

Files to modify: src/components/LoginForm.jsx
```

**4. Implementation & Testing**
```markdown
Fix Applied:
- Changed button type to "submit"

Test Run 1:
✅ Reproduction test passes
✅ All unit tests pass
```

**5. Regression Testing**
```markdown
Full Test Suite:
✅ 127/127 tests passing
✅ No regressions detected
```

**6. Documentation**
```markdown
CHANGELOG updated:
### Fixed
- Login button now properly submits form (#234)

Code comment added explaining the fix.
```

---

### Example 2: Complex Bug with Iterations

**Scenario**: Data corruption in user profiles.

**Request**:
```
Fix the bug where user profile data gets corrupted when updating
preferences. Some fields are being set to null unexpectedly.
```

**What The Workflow Does**:

**1. Reproduction**
```markdown
Reproduction:
- User updates email preferences
- Other preferences get set to null

Reproduction Test Created:
test('updating email prefs preserves other prefs', () => {
  // Currently fails
});
```

**2. Root Cause Analysis**
```markdown
Analysis:
- Traced through update logic
- Found partial object spread issue
- updatePreferences() doesn't merge, it replaces

Root Cause: Object spread missing previous values
```

**3. Fix Planning**
```markdown
Fix Plan:
- Use deep merge instead of shallow spread
- Preserve existing preferences
- Add defensive checks
```

**4. Fix-Test Loop**
```
Fix Attempt 1:
- Applied deep merge
- Test Run: ❌ Still failing

Re-analysis:
- Deep merge working, but database save is wrong
- Mongoose save() not detecting nested changes

Fix Attempt 2:
- Mark modified before save
- Test Run: ❌ Different error

Re-analysis:
- Schema validation failing
- Required field check too strict

Fix Attempt 3:
- Updated schema validation
- Use markModified correctly
- Test Run: ✅ Passes
```

**5. Regression Testing**
```markdown
Full Suite: ✅ All passing
No regressions detected
```

**Fix Iterations**: 3 attempts to get it right

---

## Custom Workflows

You can combine plugins for custom workflows.

### Example: Full Development Cycle

```
1. Use Research Plugin: Research the best approach
2. Use Feature Plugin: Implement the feature
3. Use Review Plugin: Get comprehensive review
4. Use Bugfix Plugin: Fix any issues found
5. Deploy
```

### Example: Legacy Code Modernization

```
1. Use Investigation Plugin: Understand existing code
2. Use Research Plugin: Research modern alternatives
3. Use Feature Plugin: Implement migration
4. Use Review Plugin: Ensure quality
```

---

## Hooks and Automation

### Pre-commit Hook

Create `.claude/hooks/pre-commit.yaml`:

```yaml
name: pre-commit
description: Runs before committing changes

actions:
  - name: run_tests
    command: npm test
    on_failure: block

  - name: lint
    command: npm run lint
    on_failure: warn

  - name: format
    command: npm run format
    auto_fix: true
```

### Post-Test Hook

Create `.claude/hooks/post-test.yaml`:

```yaml
name: post-test
description: Runs after test execution

triggers:
  - event: test_completed

actions:
  - name: coverage_check
    condition: coverage.percentage < 80
    action: warn
    message: "Coverage below 80%: {{coverage.percentage}}%"

  - name: update_badge
    command: npm run update-badge
```

### On-Review-Fail Hook

```yaml
name: on-review-fail
description: Automated actions when review finds issues

triggers:
  - event: review_completed
    condition: review.issues.critical > 0

actions:
  - name: create_tasks
    type: todo
    items:
      - "Fix critical security issue: {{issue.description}}"

  - name: notify
    type: notification
    message: "⚠️ Critical issues found in review"
```

---

## Best Practices

1. **Start with Investigation**: Understand before changing
2. **Research First**: Don't reinvent the wheel
3. **Use Review Plugin**: Catch issues early
4. **Let Loops Run**: The iterations improve quality
5. **Customize Configs**: Adapt to your project needs
6. **Document Findings**: Research and investigations are reusable

---

## Troubleshooting

### Workflow Stuck in Loop

If a workflow is iterating too many times:
1. Check `max_iterations` in config
2. Review what's blocking approval
3. May need manual intervention

### Tests Keep Failing

If bugfix plugin can't fix tests:
1. Problem may be more complex than automated fix
2. Review the analysis to understand root cause
3. Consider manual debugging

### Review Too Strict

If reviews block too often:
1. Adjust `strictness` in review config
2. Disable optional review perspectives
3. Customize review criteria

---

## Next Steps

1. Try each plugin with simple examples
2. Customize configurations for your project
3. Create custom hooks for your workflow
4. Contribute improvements back to marketplace

For more examples, see the `examples/` directory in each plugin.

# Bugfix Workflow

Systematic bug fixing with root cause analysis and comprehensive testing loops.

## Input Parameters

```yaml
bug_description: "Description of the bug"
bug_id: "Optional bug ID (e.g., GH-123)"
reproduction_steps: "Steps to reproduce (optional)"
max_fix_iterations: 3
require_regression_tests: true
```

## Workflow State

```yaml
state:
  phase: "reproduction | analysis | planning | fixing | regression | review | documentation"
  bug_reproduced: false
  reproduction_test_created: false
  root_cause_identified: false
  fix_iteration: 0
  fix_works: false
  regression_detected: false
  changes_made: []
```

---

## Phase 1: Bug Reproduction

### Step 1.1: Reproduce Bug

**Use TodoWrite**: "Reproduce the reported bug"

1. **Analyze Bug Report**
   - Read `bug_description`
   - Extract expected vs actual behavior
   - Note reproduction steps if provided

2. **Attempt Reproduction**
   - Follow reproduction steps
   - Try to trigger the bug
   - Document what happens

3. **Verify Bug Exists**
   - Confirm the bug is reproducible
   - Note any conditions needed

**If can't reproduce**:
   - Document what was tried
   - Ask user for more details
   - Stop workflow

**Output**: `state.bug_reproduced = true`

---

### Step 1.2: Create Reproduction Test

**Use TodoWrite**: "Create test that demonstrates the bug"

Write a failing test that reliably reproduces the bug:

```javascript
// Example
test('bug: login fails with special characters in password', () => {
  const result = login('user@example.com', 'p@ssw0rd!');
  expect(result.success).toBe(true); // Currently fails
});
```

**Output**: `state.reproduction_test_created = true`

Mark reproduction complete, add "Analyze root cause"

---

## Phase 2: Root Cause Analysis

### Step 2.1: Analyze Bug

**Use TodoWrite**: "Analyze bug and identify root cause"

Invoke **Bug Analyzer subagent** (`subagents/bug-analyzer.md`):

1. **Examine Error Messages**
   - Read stack traces
   - Identify error types
   - Note error locations

2. **Read Relevant Code**
   - Find the code that's failing
   - Understand what it's trying to do
   - Identify where it goes wrong

3. **Trace Execution**
   - Follow the code path
   - Identify where bug originates
   - Distinguish symptom from cause

4. **Identify Root Cause**
   - What is the ACTUAL problem?
   - Why does this code fail?
   - What assumption is wrong?

**Output**:
- `root_cause`: Detailed explanation
- `affected_code`: Specific files and lines
- `category`: Type of bug (logic, validation, edge case, etc.)

**Output**: `state.root_cause_identified = true`

Mark analysis complete, add "Plan bug fix"

---

## Phase 3: Fix Planning

### Step 3.1: Plan Fix

**Use TodoWrite**: "Plan the bug fix approach"

Based on root cause analysis:

1. **Design Fix**
   - What code needs to change?
   - What is the minimal fix?
   - What is the most robust fix?

2. **Consider Alternatives**
   - Are there multiple ways to fix this?
   - Which approach is best?
   - Trade-offs of each approach?

3. **Assess Impact**
   - What else might this change affect?
   - Are there similar bugs elsewhere?
   - Will this break anything?

4. **Choose Approach**
   - Select the best fix approach
   - Document why this approach was chosen

**Output**:
- `fix_plan`: Detailed plan
- `files_to_modify`: List of files
- `expected_impact`: What will change

Mark planning complete, add "Implement bug fix"

---

## Phase 4: Fix Implementation (with Testing Loop)

### Step 4.1: Implement Fix

**Use TodoWrite**: "Implement bug fix"

Implement the planned fix:

1. **Make Changes**
   - Modify the identified files
   - Follow the fix plan
   - Add defensive checks if appropriate

2. **Add Comments**
   - Explain WHY the bug occurred
   - Document the fix approach

**Output**: `state.changes_made` updated

Mark implementation complete, add "Test bug fix"

---

### Step 4.2: Test Fix

**Use TodoWrite**: "Test bug fix"

Run tests to verify fix:

1. **Run Reproduction Test**
   - The test created in Phase 1
   - Should now PASS

2. **Run Related Unit Tests**
   - Tests for the affected functionality
   - Should all PASS

**Output**:
- `test_results`: Detailed results
- `state.fix_works`: true if all pass, false otherwise

---

### Step 4.3: Check Fix Status

**If** `state.fix_works`:
- Mark testing complete
- **Proceed to Phase 5: Regression Testing**

**If** `!state.fix_works && state.fix_iteration >= max_fix_iterations`:
- Mark as "Fix still not working after max iterations"
- **Create summary** of what was tried
- **Ask user** for guidance
- **Stop workflow**

**If** `!state.fix_works && state.fix_iteration < max_fix_iterations`:
- **Proceed to Step 4.4: Re-analyze**

---

### Step 4.4: Re-analyze Failure

**Use TodoWrite**: "Re-analyze why fix didn't work"

The fix didn't work. Analyze why:

1. **Review Test Results**
   - What is still failing?
   - Same error or new error?

2. **Re-examine Fix**
   - Did we fix the right thing?
   - Is there a deeper issue?
   - Did we introduce a new bug?

3. **Revise Understanding**
   - Was our root cause analysis wrong?
   - Is there another layer to this bug?

**Output**: Revised understanding and new fix approach

Mark re-analysis complete, add "Apply revised fix"

---

### Step 4.5: Apply Revised Fix

**Use TodoWrite**: "Apply revised fix"

Based on re-analysis, adjust the fix:

1. **Revise Changes**
   - Update the fix based on new understanding
   - May need to undo previous changes

2. **Update Approach**
   - Try a different approach if needed

**Output**: Updated `state.changes_made`

Increment `state.fix_iteration`

Mark revision complete

**Action**: Return to Step 4.2 (Test Fix)

---

## Phase 5: Regression Testing

### Step 5.1: Run Full Test Suite

**Use TodoWrite**: "Run full test suite for regressions"

Ensure the fix doesn't break anything:

1. **Run All Tests**
   - Complete test suite
   - All test files

2. **Compare Results**
   - Any new failures?
   - Tests that passed before but fail now = regression

**Output**:
- `all_tests_passed`: boolean
- `regressions`: array of tests that now fail

---

### Step 5.2: Handle Regressions

**If** `all_tests_passed`:
- Mark regression testing complete
- **Proceed to Phase 6: Code Review**

**If** regressions detected:

**Use TodoWrite**: "Fix regressions introduced by bug fix"

1. **Analyze Regressions**
   - What is now broken?
   - How is it related to our fix?

2. **Adjust Fix**
   - Modify fix to avoid breaking other functionality
   - May need to refactor approach

3. **Re-test**
   - Run full suite again
   - Verify regressions resolved

**Action**: Return to Step 5.1 if adjustments made

---

## Phase 6: Code Review

### Step 6.1: Review Fix

**Use TodoWrite**: "Review bug fix code quality"

Review the fix for quality:

1. **Quality Review**
   - Is the code clean?
   - Is it the minimal fix?
   - Are there better approaches?

2. **Defensive Checks**
   - Should we add validation?
   - Should we add error handling?
   - Should we add logging?

3. **Similar Bugs**
   - Could this bug exist elsewhere?
   - Should we fix similar patterns?

4. **Future Prevention**
   - How can we prevent this in the future?
   - Should we add automated checks?

**Output**: Review feedback and any additional changes needed

Mark review complete, add "Create bug fix documentation"

---

## Phase 7: Documentation

### Step 7.1: Document Fix

**Use TodoWrite**: "Document the bug fix"

Create documentation:

1. **Update CHANGELOG**
   ```markdown
   ### Fixed
   - Login now works correctly with special characters in passwords (#123)
   ```

2. **Add Code Comments**
   - Explain why the bug occurred
   - Document the fix approach
   - Warn about potential pitfalls

3. **Create Bug Report Summary** (optional)
   ```markdown
   # Bug Fix: Login with Special Characters

   ## Bug Description
   Users could not log in when password contained special characters.

   ## Root Cause
   Password validation was URL-encoding the password before comparison,
   but the stored password was not encoded.

   ## Fix
   Removed URL-encoding step from validation logic.

   ## Files Changed
   - src/auth/login.js

   ## Tests Added
   - tests/auth/special-chars.test.js

   ## Lessons Learned
   - Always test with special characters
   - Ensure encoding/decoding is symmetric
   ```

Mark documentation complete

---

## Workflow Complete

Present final summary:

```markdown
# Bug Fix Complete ✅

## Bug
{{bug_description}}

## Root Cause
{{root_cause}}

## Summary
- Fix iterations: {{state.fix_iteration}}
- Regressions detected: {{state.regression_detected}}
- Files changed: {{state.changes_made.length}}

## Changes Made
{{state.changes_made}}

## Tests
- Reproduction test: ✅ Passing
- Unit tests: ✅ Passing
- Full suite: ✅ Passing (no regressions)

## Next Steps
1. Merge the fix
2. Deploy to production
3. Monitor for related issues

## Deliverables
- [x] Bug reproduced
- [x] Root cause identified
- [x] Fix implemented
- [x] Tests passing
- [x] No regressions
- [x] Code reviewed
- [x] Documentation updated
```

---

## Error Handling

### Cannot Reproduce Bug
- Document all attempts
- Request additional information from user:
  - Exact steps to reproduce
  - Environment details (browser, OS, etc.)
  - Screenshots or error messages
  - When bug started occurring

### Fix Doesn't Work After Max Iterations
- Summarize all approaches tried
- Document current understanding
- Recommend:
  - Pair programming session
  - Deeper investigation needed
  - May be a more complex issue
  - Consider refactoring affected area

### Persistent Regressions
- May indicate fix approach is wrong
- Consider:
  - Refactoring instead of patching
  - Broader architectural change needed
  - Tests may have false positives

---

## Best Practices

1. **Always reproduce first** - Don't fix what you can't reproduce
2. **Find root cause** - Don't treat symptoms
3. **Write tests** - Regression prevention is key
4. **Minimal changes** - Smallest fix that works
5. **Document why** - Help future developers understand
6. **Test thoroughly** - Unit tests AND full suite
7. **Learn from bugs** - Document lessons learned

---

## Customization

Customize this workflow by:
- Adjusting `max_fix_iterations`
- Adding performance testing after fix
- Integrating with issue tracking systems
- Adding automated bug reporting
- Creating bug pattern library

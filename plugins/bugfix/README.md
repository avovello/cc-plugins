# Bugfix Plugin 🐛

Systematic bug fixing with reproduction, root cause analysis, and comprehensive regression testing.

## Installation
```bash
/plugin install bugfix
```

## Usage
```bash
/bugfix "Users can't upload files larger than 5MB"

# With E2E browser testing (for UI-related bugs)
/bugfix "Login button not responding" --e2e
```

## Specialized Agents (7)
1. **bug-reproducer**: Reproduces bugs, creates failing tests
2. **root-cause-analyst**: Identifies root causes
3. **impact-assessor**: Assesses severity and impact
4. **fix-planner**: Plans fix approach
5. **fix-implementer**: Implements fixes
6. **fix-tester**: Tests fixes
7. **regression-tester**: Runs regression tests

## Processing Loop
Fix → Test → (if fail) → Re-analyze → Re-fix → Re-test (max 3 iterations)

## Workflow
Reproduce → Analyze → Plan → Fix Loop → Regression Test → (E2E)

## E2E Browser Testing

For UI-related bugs, use the `--e2e` flag to include browser testing via the **QA plugin**:

| QA Plugin Command | Purpose |
|-------------------|---------|
| `/e2e` | Run Playwright browser tests after fix |
| `browser-tester` agent | Manually verify UI bug is fixed |
| `/write-tests --e2e` | Generate E2E regression tests |

## Related Plugins
- **QA Plugin**: Comprehensive testing workflows (`/qa`, `/e2e`, `/unit`, `/integration`)

## Version
1.0.0

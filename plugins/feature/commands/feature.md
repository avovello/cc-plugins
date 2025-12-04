# Feature Command

**Alias for `/feature-dev`** - Complete feature development lifecycle.

## Usage

```bash
/feature "Add user authentication with OAuth2"

# With E2E browser testing (for UI features)
/feature "Add shopping cart UI" --e2e
```

## Workflow

This command follows the 7-phase workflow. See `/feature-dev` for full documentation.

```
Phase 1: Discovery → Phase 2: Exploration → Phase 3: Questions
    → Phase 4: Architecture → Phase 5: Implementation
    → Phase 6: QA-DEV-REVIEW Loop → Phase 7: Summary
```

## Specialized Agents (6)

| Agent | Purpose |
|-------|---------|
| `code-explorer` | Deep codebase analysis, traces execution paths |
| `code-architect` | Architecture design with implementation blueprint |
| `code-reviewer` | Precision review with confidence filtering (80+) |
| `test-writer` | Comprehensive test suites (80%+ coverage) |
| `test-runner` | Test execution with test-fix-retest loop |
| `implementation-documenter` | Feature documentation |

## Processing Loops (QA-DEV-REVIEW)

- **Testing Loop**: Test → Analyze → Fix → Re-test (max 3 iterations)
- **Review Loop**: Review → Fix → Re-review (max 2 iterations)

## E2E Testing Integration

For UI features, use `--e2e` flag to include browser testing via QA plugin.

> **See also**: `/feature-dev` for detailed workflow documentation

## Version

1.1.0

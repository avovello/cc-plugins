# Feature Development Command

**Purpose**: Complete feature development lifecycle with planning, implementation, testing loops, review loops, and documentation

## Workflow

1. **Architecture Planning**: Plan architecture changes
2. **Implementation Planning**: Plan implementation steps
3. **Code Implementation**: Write production code
4. **Test Writing**: Write tests
5. **Testing Loop**: Test → (if fail) → Analyze → Fix → Re-test (max 3 iterations)
6. **E2E Testing** (optional): Browser testing for UI features → uses QA plugin
7. **Code Quality Review**: Review code quality
8. **Review Loop**: Review → (if changes requested) → Apply feedback → Re-review (max 2 iterations)
9. **Documentation**: Create/update documentation

## Specialized Agents (8)
- architecture-planner: Plans architecture changes
- implementation-planner: Plans implementation steps
- code-implementer: Writes code
- test-writer: Writes tests
- test-runner: Runs tests
- test-failure-analyzer: Analyzes test failures
- code-quality-reviewer: Reviews code
- implementation-documenter: Documents implementation

## Usage
```bash
/feature "Add user authentication with OAuth2"

# With E2E browser testing (for UI features)
/feature "Add user authentication with OAuth2" --e2e
```

## Processing Loops
- **Testing Loop**: Repeats until tests pass (max 3 times)
- **Review Loop**: Repeats until approved (max 2 times)

## E2E Testing Integration

For features with UI components, use the `--e2e` flag to include browser testing:

```bash
/feature "Add shopping cart" --e2e
```

This will additionally run the **QA plugin** commands:
- `/e2e` - Execute Playwright browser tests
- Interactive testing via `browser-tester` agent (Playwright MCP)

> **See also**: QA plugin for standalone testing workflows (`/qa`, `/e2e`, `/unit`, `/integration`, `/write-tests`)

## Version
1.0.0

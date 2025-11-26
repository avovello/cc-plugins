# Feature Plugin ✨

Complete feature development lifecycle with planning, implementation, testing loops, code review loops, and documentation.

## Installation
```bash
/plugin install feature
```

## Usage
```bash
/feature "Add real-time notifications with WebSockets"

# With E2E browser testing (for UI features)
/feature "Add shopping cart UI" --e2e
```

## Specialized Agents (8)
1. **architecture-planner**: Architecture planning
2. **implementation-planner**: Implementation planning
3. **code-implementer**: Code writing
4. **test-writer**: Test creation
5. **test-runner**: Test execution
6. **test-failure-analyzer**: Failure analysis
7. **code-quality-reviewer**: Code review
8. **implementation-documenter**: Documentation

## Processing Loops
- **Testing Loop**: Test → Analyze → Fix → Re-test (max 3 iterations)
- **Review Loop**: Review → Fix → Re-review (max 2 iterations)

## Workflow
Plan → Implement → Test Loop → (E2E) → Review Loop → Document

## E2E Browser Testing

For UI-heavy features, use the `--e2e` flag to include browser testing via the **QA plugin**:

| QA Plugin Command | Purpose |
|-------------------|---------|
| `/e2e` | Run Playwright browser tests |
| `/write-tests --e2e` | Generate E2E test code |
| `browser-tester` agent | Interactive testing via Playwright MCP |

## Related Plugins
- **QA Plugin**: Standalone testing workflows (`/qa`, `/e2e`, `/unit`, `/integration`, `/write-tests`)

## Version
1.0.0

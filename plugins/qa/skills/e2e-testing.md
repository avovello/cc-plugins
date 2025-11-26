# E2E Testing Skill

End-to-end testing capability using Playwright MCP for browser automation.

## When to Use

- Testing complete user workflows
- Validating UI interactions
- Cross-browser testing
- Visual regression testing
- Accessibility validation

## Capabilities

### Interactive Browser Testing

Use **Playwright MCP** tools for real browser interaction:

```
mcp__playwright__browser_navigate    - Go to URL
mcp__playwright__browser_click       - Click elements
mcp__playwright__browser_fill        - Fill form fields
mcp__playwright__browser_screenshot  - Capture screenshots
mcp__playwright__browser_evaluate    - Run JavaScript
mcp__playwright__browser_snapshot    - Accessibility tree
```

### Automated Test Execution

Run Playwright test suites:

```bash
# Run all E2E tests
npx playwright test

# Run specific test
npx playwright test tests/e2e/login.spec.ts

# Run in headed mode
npx playwright test --headed

# Run specific browser
npx playwright test --project=chromium
```

## Workflow

1. **Explore** - Use `browser-tester` agent to manually test flows
2. **Record** - Document actions during exploration
3. **Generate** - Use `test-writer` agent to create test code
4. **Execute** - Use `test-runner` agent to run tests

## Quick Reference

### Navigate and Interact

```
Navigate: mcp__playwright__browser_navigate → URL
Click: mcp__playwright__browser_click → selector
Fill: mcp__playwright__browser_fill → selector, value
Screenshot: mcp__playwright__browser_screenshot → name
```

### Common Selectors

```
By role: button, link, textbox, checkbox
By text: text=Submit, text=Login
By test ID: [data-testid="submit-btn"]
By CSS: .class-name, #id-name
```

## Integration with QA Plugin

- **Agent**: `browser-tester` - Interactive testing
- **Agent**: `test-writer` - Generate Playwright tests
- **Agent**: `test-runner` - Execute test suites
- **Command**: `/e2e` - Run E2E test workflow

---
description: Run end-to-end tests using Playwright for browser automation
---

# E2E Command

Runs end-to-end tests using Playwright for browser automation.

## Usage

```bash
/e2e                       # Run all E2E tests
/e2e [test-file]           # Run specific test file
/e2e [pattern]             # Run tests matching pattern
/e2e --headed              # Run with visible browser
/e2e --browser=firefox     # Run in specific browser
/e2e --debug               # Run in debug mode
```

## What It Does

The `/e2e` command executes Playwright-based end-to-end tests:

1. **Detect Configuration** - Find playwright.config.ts
2. **Start Dev Server** - If configured in playwright config
3. **Run Tests** - Execute E2E test suites
4. **Generate Report** - HTML report with screenshots/videos
5. **Report Results** - Summary with failures and artifacts

## Agents Used

| Agent | Purpose |
|-------|---------|
| `test-runner` | Execute Playwright test suite |

## Browser Options

```bash
/e2e --browser=chromium    # Google Chrome
/e2e --browser=firefox     # Mozilla Firefox
/e2e --browser=webkit      # Apple Safari
/e2e --browser=all         # All browsers
```

## Output

```markdown
# E2E Test Report

## Summary
- Total: 24 tests
- Passed: 22 (91.7%)
- Failed: 2 (8.3%)
- Duration: 2m 15s

## Browser Results
| Browser | Passed | Failed |
|---------|--------|--------|
| Chromium | 23 | 1 |
| Firefox | 22 | 2 |
| WebKit | 24 | 0 |

## Failed Tests
1. login.spec.ts:23 - Timeout waiting for dashboard
   Screenshot: test-results/login-failed.png

## View Report
npx playwright show-report
```

## Options

| Option | Description |
|--------|-------------|
| `--headed` | Show browser window |
| `--debug` | Run in debug mode |
| `--browser=<name>` | Specific browser |
| `--workers=<n>` | Parallel workers |
| `--retries=<n>` | Retry failed tests |
| `--ui` | Interactive UI mode |

## Examples

```bash
# Run all E2E tests
/e2e

# Run specific test file
/e2e tests/e2e/auth/login.spec.ts

# Run tests matching pattern
/e2e auth

# Debug failing test
/e2e login.spec.ts --debug --headed

# Run in specific browser
/e2e --browser=firefox

# Interactive mode
/e2e --ui
```

## Interactive Testing

For manual browser exploration before writing tests:

```bash
/e2e --explore [url]
```

This uses the `browser-tester` agent with Playwright MCP for interactive testing.

# QA Plugin

Quality Assurance workflows with browser testing (Playwright MCP), test writing, and comprehensive test execution for unit, integration, and E2E testing.

## Features

- **Browser Testing** - Interactive testing via Playwright MCP
- **Test Generation** - Automated test code creation
- **Multi-Level Testing** - Unit, integration, and E2E support
- **Coverage Tracking** - Code coverage reports
- **Cross-Browser** - Chromium, Firefox, WebKit support

## Quick Start

```bash
# Run full QA suite
/qa

# Run specific test types
/unit              # Unit tests
/integration       # Integration tests
/e2e               # End-to-end tests

# Generate tests
/write-tests --unit src/services/user.ts
```

## Commands

| Command | Description |
|---------|-------------|
| `/qa` | Full QA workflow (unit → integration → E2E) |
| `/unit` | Run unit tests with coverage |
| `/integration` | Run API/database integration tests |
| `/e2e` | Run Playwright E2E tests |
| `/write-tests` | Generate test code |

## Agents

| Agent | Purpose |
|-------|---------|
| `browser-tester` | Interactive browser testing via Playwright MCP |
| `test-writer` | Generate unit, integration, and E2E tests |
| `test-runner` | Execute test suites and report results |

## Skills

| Skill | Description |
|-------|-------------|
| `e2e-testing` | Playwright MCP browser automation |
| `unit-testing` | Isolated function/component testing |
| `integration-testing` | API, database, service testing |

## Workflow

### Full QA Pipeline

```
/qa
 ├── Unit Tests (fast, ~10s)
 ├── Integration Tests (medium, ~1-2min)
 └── E2E Tests (comprehensive, ~3-5min)
```

### Browser Testing → Test Generation

```bash
# 1. Explore interactively
/e2e --explore https://app.example.com

# 2. Record actions during testing
# (browser-tester captures actions)

# 3. Generate automated tests
/write-tests --from-actions

# 4. Run generated tests
/e2e
```

## Playwright MCP Integration

The `browser-tester` agent uses Playwright MCP tools for real browser interaction:

```
mcp__playwright__browser_navigate    - Navigate to URL
mcp__playwright__browser_click       - Click elements
mcp__playwright__browser_fill        - Fill form fields
mcp__playwright__browser_screenshot  - Capture screenshots
mcp__playwright__browser_evaluate    - Run JavaScript
```

## Framework Support

| Language | Unit | Integration | E2E |
|----------|------|-------------|-----|
| JavaScript/TypeScript | Jest, Vitest | supertest | Playwright |
| Python | pytest | pytest | Playwright |
| PHP | PHPUnit | PHPUnit | Playwright |

## Requirements

- Node.js 18+ (for Playwright)
- Test framework installed (Jest/Vitest/pytest/PHPUnit)
- Playwright MCP configured (for browser testing)

## Installation

```bash
# Install Playwright
npm init playwright@latest

# Install test framework
npm install -D jest @types/jest
# or
npm install -D vitest
```

## Version

- **Version**: 0.2.0
- **Category**: Quality
- **Keywords**: qa, testing, playwright, unit-tests, integration-tests, e2e, browser-testing

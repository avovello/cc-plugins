---
name: e2e-testing
description: End-to-end testing and interactive browser testing with Playwright. Use when running, writing, or reviewing E2E tests, testing user workflows in a browser, exploring web apps interactively, recording browser actions, cross-browser testing, visual validation, or converting recorded actions to automated tests.
---

# E2E Testing

Guide for teammates working with end-to-end tests and browser testing — writing, running, exploring, or reviewing.

## Running Tests

```bash
npx playwright test                           # Run all E2E tests
npx playwright test tests/e2e/login.spec.ts   # Run specific test
npx playwright test --headed                  # With visible browser
npx playwright test --project=chromium        # Specific browser
```

## Writing Tests

- Use Page Object Model for selectors
- Test complete user workflows end-to-end
- Wait for network idle before assertions
- Capture screenshots at key assertion points

### Selector Priority

1. `[data-testid="..."]` — most stable
2. Role-based: `getByRole('button', { name: '...' })`
3. Text-based: `getByText('...')`
4. CSS selectors — last resort

## Interactive Browser Exploration

When exploring a web app interactively, use built-in browser tools:

1. Navigate to target URL
2. Take accessibility snapshot to understand page structure
3. Interact with elements (fill forms, click buttons)
4. Validate outcomes (check content, URLs, element state)
5. Capture screenshots at key states
6. Check console for JavaScript errors

### Recording Actions

Document all actions in structured format for conversion to automated tests:

```
## Recorded Actions: [Test Name]
1. navigate: /page
2. fill: input[name="field"] = "value"
3. click: button[type="submit"]
4. wait: .success-message
5. assert: URL contains "/expected"
6. screenshot: descriptive-name
```

## Responsive Testing

Test at multiple viewport sizes: Mobile (375x667), Tablet (768x1024), Desktop (1920x1080).

## Reviewing Tests

Check for:
- Complete user workflow coverage (not just click tests)
- Stable selectors (data-testid preferred)
- Proper waits (no arbitrary sleep)
- Screenshot evidence at key states
- Console error checking
- Responsive viewport coverage

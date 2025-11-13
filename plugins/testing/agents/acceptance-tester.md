# Acceptance Tester Agent (E2E with Playwright + TypeScript)

## Purpose

Runs end-to-end acceptance tests using **Playwright + TypeScript** to test real web applications in actual browsers, validating complete user workflows.

## Responsibilities

✅ **DOES**:
- Run E2E acceptance tests with Playwright
- Test in real browsers (Chromium, Firefox, WebKit)
- Capture screenshots and videos on failure
- Generate HTML test reports
- Test responsive design across devices
- Validate accessibility
- Test complete user journeys

❌ **DOES NOT**:
- Run unit tests (that's unit-tester)
- Run integration tests (that's integration-tester)
- Test individual functions (use unit tests)

## Tools Available

- Bash: Run Playwright commands
- Read: Read test configuration
- Write: Generate test reports

## Playwright + TypeScript Setup

### Installation

```bash
# Install Playwright with TypeScript support
npm init playwright@latest

# Or add to existing project
npm install -D @playwright/test
npm install -D typescript @types/node

# Install browsers
npx playwright install
```

### Configuration

```typescript
// playwright.config.ts
import { defineConfig, devices } from '@playwright/test';

export default defineConfig({
  testDir: './tests/e2e',
  fullyParallel: true,
  forbidOnly: !!process.env.CI,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 1 : undefined,
  reporter: [
    ['html'],
    ['json', { outputFile: 'test-results/results.json' }],
    ['junit', { outputFile: 'test-results/junit.xml' }]
  ],
  use: {
    baseURL: 'http://localhost:3000',
    trace: 'on-first-retry',
    screenshot: 'only-on-failure',
    video: 'retain-on-failure',
  },
  projects: [
    {
      name: 'chromium',
      use: { ...devices['Desktop Chrome'] },
    },
    {
      name: 'firefox',
      use: { ...devices['Desktop Firefox'] },
    },
    {
      name: 'webkit',
      use: { ...devices['Desktop Safari'] },
    },
    // Mobile browsers
    {
      name: 'Mobile Chrome',
      use: { ...devices['Pixel 5'] },
    },
    {
      name: 'Mobile Safari',
      use: { ...devices['iPhone 12'] },
    },
  ],
  webServer: {
    command: 'npm run start',
    url: 'http://localhost:3000',
    reuseExistingServer: !process.env.CI,
  },
});
```

## Example E2E Tests with TypeScript

### 1. Basic Page Navigation

```typescript
// tests/e2e/navigation.spec.ts

import { test, expect } from '@playwright/test';

test.describe('Navigation', () => {
  test('should navigate to different pages', async ({ page }) => {
    // Go to homepage
    await page.goto('/');
    await expect(page).toHaveTitle(/Home/);

    // Navigate to about page
    await page.click('text=About');
    await expect(page).toHaveURL('/about');
    await expect(page.locator('h1')).toContainText('About Us');

    // Navigate to contact page
    await page.getByRole('link', { name: 'Contact' }).click();
    await expect(page).toHaveURL('/contact');
  });

  test('should show 404 for invalid routes', async ({ page }) => {
    const response = await page.goto('/nonexistent-page');
    expect(response?.status()).toBe(404);
    await expect(page.locator('h1')).toContainText('404');
  });
});
```

### 2. User Authentication Flow

```typescript
// tests/e2e/auth.spec.ts

import { test, expect, type Page } from '@playwright/test';

test.describe('Authentication', () => {
  test.describe.configure({ mode: 'serial' });

  let page: Page;

  test.beforeAll(async ({ browser }) => {
    page = await browser.newPage();
  });

  test.afterAll(async () => {
    await page.close();
  });

  test('should register new user', async () => {
    await page.goto('/register');

    // Fill registration form
    await page.getByLabel('Email').fill('test@example.com');
    await page.getByLabel('Password').fill('SecurePass123!');
    await page.getByLabel('Confirm Password').fill('SecurePass123!');
    await page.getByLabel('Name').fill('Test User');

    // Submit form
    await page.getByRole('button', { name: 'Sign Up' }).click();

    // Verify redirect to dashboard
    await expect(page).toHaveURL('/dashboard');
    await expect(page.locator('.welcome-message')).toContainText('Welcome, Test User');
  });

  test('should login with credentials', async () => {
    await page.goto('/login');

    await page.fill('input[name="email"]', 'test@example.com');
    await page.fill('input[type="password"]', 'SecurePass123!');
    await page.click('button[type="submit"]');

    // Wait for navigation
    await page.waitForURL('/dashboard');

    // Verify logged in
    await expect(page.locator('.user-profile')).toBeVisible();
  });

  test('should show error for invalid credentials', async () => {
    await page.goto('/login');

    await page.fill('input[name="email"]', 'wrong@example.com');
    await page.fill('input[type="password"]', 'wrongpassword');
    await page.click('button[type="submit"]');

    // Should show error message
    await expect(page.locator('.error-message')).toContainText('Invalid credentials');

    // Should stay on login page
    await expect(page).toHaveURL('/login');
  });

  test('should logout successfully', async () => {
    // Assuming already logged in from previous tests
    await page.goto('/dashboard');

    await page.click('button.logout-button');

    // Should redirect to home
    await expect(page).toHaveURL('/');

    // Should not be able to access dashboard
    await page.goto('/dashboard');
    await expect(page).toHaveURL('/login');
  });
});
```

### 3. Form Submission with File Upload

```typescript
// tests/e2e/file-upload.spec.ts

import { test, expect } from '@playwright/test';
import path from 'path';

test.describe('File Upload', () => {
  test('should upload file successfully', async ({ page }) => {
    await page.goto('/upload');

    // Select file
    const filePath = path.join(__dirname, 'fixtures', 'test-document.pdf');
    await page.setInputFiles('input[type="file"]', filePath);

    // Verify file selected
    await expect(page.locator('.selected-file')).toContainText('test-document.pdf');

    // Add description
    await page.fill('textarea[name="description"]', 'Test document upload');

    // Submit
    await page.click('button[type="submit"]');

    // Wait for upload to complete
    await expect(page.locator('.success-message')).toBeVisible();
    await expect(page.locator('.success-message')).toContainText('File uploaded successfully');

    // Verify file appears in list
    await expect(page.locator('.file-list')).toContainText('test-document.pdf');
  });

  test('should reject files over size limit', async ({ page }) => {
    await page.goto('/upload');

    // Try to upload large file
    const largePath = path.join(__dirname, 'fixtures', 'large-file.pdf');
    await page.setInputFiles('input[type="file"]', largePath);

    await page.click('button[type="submit"]');

    // Should show error
    await expect(page.locator('.error-message')).toContainText('File too large');
  });
});
```

### 4. API Interaction & Data Loading

```typescript
// tests/e2e/data-loading.spec.ts

import { test, expect } from '@playwright/test';

test.describe('Data Loading', () => {
  test('should load and display data from API', async ({ page }) => {
    // Mock API response
    await page.route('**/api/users', async (route) => {
      await route.fulfill({
        status: 200,
        contentType: 'application/json',
        body: JSON.stringify({
          users: [
            { id: 1, name: 'John Doe', email: 'john@example.com' },
            { id: 2, name: 'Jane Smith', email: 'jane@example.com' }
          ]
        })
      });
    });

    await page.goto('/users');

    // Verify data loaded
    await expect(page.locator('.user-card')).toHaveCount(2);
    await expect(page.locator('.user-card').first()).toContainText('John Doe');
  });

  test('should handle API errors gracefully', async ({ page }) => {
    // Mock API error
    await page.route('**/api/users', async (route) => {
      await route.fulfill({
        status: 500,
        contentType: 'application/json',
        body: JSON.stringify({ error: 'Internal Server Error' })
      });
    });

    await page.goto('/users');

    // Should show error message
    await expect(page.locator('.error-banner')).toBeVisible();
    await expect(page.locator('.error-banner')).toContainText('Failed to load users');
  });

  test('should show loading state', async ({ page }) => {
    // Delay API response
    await page.route('**/api/users', async (route) => {
      await new Promise(resolve => setTimeout(resolve, 1000));
      await route.fulfill({
        status: 200,
        body: JSON.stringify({ users: [] })
      });
    });

    await page.goto('/users');

    // Should show loading spinner
    await expect(page.locator('.loading-spinner')).toBeVisible();

    // Loading should disappear after data loads
    await expect(page.locator('.loading-spinner')).not.toBeVisible();
  });
});
```

### 5. Responsive Design Testing

```typescript
// tests/e2e/responsive.spec.ts

import { test, expect, devices } from '@playwright/test';

test.describe('Responsive Design', () => {
  test('should display mobile menu on small screens', async ({ page }) => {
    // Set viewport to mobile size
    await page.setViewportSize({ width: 375, height: 667 });
    await page.goto('/');

    // Desktop menu should be hidden
    await expect(page.locator('.desktop-nav')).not.toBeVisible();

    // Mobile menu button should be visible
    await expect(page.locator('.mobile-menu-button')).toBeVisible();

    // Click mobile menu
    await page.click('.mobile-menu-button');

    // Mobile menu should open
    await expect(page.locator('.mobile-nav')).toBeVisible();
  });

  test('should adapt layout for tablet', async ({ page }) => {
    await page.setViewportSize({ width: 768, height: 1024 });
    await page.goto('/products');

    // Should show 2 columns on tablet
    const grid = page.locator('.product-grid');
    await expect(grid).toHaveCSS('grid-template-columns', /repeat\(2,/);
  });

  test.use({ ...devices['iPhone 12'] });
  test('should work on iPhone 12', async ({ page }) => {
    await page.goto('/');
    await expect(page.locator('h1')).toBeVisible();

    // Test touch interactions
    await page.tap('.mobile-menu-button');
    await expect(page.locator('.mobile-nav')).toBeVisible();
  });
});
```

### 6. Accessibility Testing

```typescript
// tests/e2e/accessibility.spec.ts

import { test, expect } from '@playwright/test';
import AxeBuilder from '@axe-core/playwright';

test.describe('Accessibility', () => {
  test('should not have accessibility violations', async ({ page }) => {
    await page.goto('/');

    const accessibilityScanResults = await new AxeBuilder({ page }).analyze();

    expect(accessibilityScanResults.violations).toEqual([]);
  });

  test('should be keyboard navigable', async ({ page }) => {
    await page.goto('/');

    // Tab through elements
    await page.keyboard.press('Tab');
    await expect(page.locator('a').first()).toBeFocused();

    await page.keyboard.press('Tab');
    await expect(page.locator('a').nth(1)).toBeFocused();

    // Enter should activate links
    await page.keyboard.press('Enter');
    await expect(page).not.toHaveURL('/');
  });

  test('should have proper ARIA labels', async ({ page }) => {
    await page.goto('/');

    // Check for proper labels
    await expect(page.locator('button').first()).toHaveAttribute('aria-label');
    await expect(page.locator('nav')).toHaveAttribute('aria-label', 'Main navigation');
  });
});
```

### 7. Page Object Model Pattern

```typescript
// tests/e2e/pages/LoginPage.ts

import { type Page, type Locator } from '@playwright/test';

export class LoginPage {
  readonly page: Page;
  readonly emailInput: Locator;
  readonly passwordInput: Locator;
  readonly submitButton: Locator;
  readonly errorMessage: Locator;

  constructor(page: Page) {
    this.page = page;
    this.emailInput = page.locator('input[name="email"]');
    this.passwordInput = page.locator('input[type="password"]');
    this.submitButton = page.locator('button[type="submit"]');
    this.errorMessage = page.locator('.error-message');
  }

  async goto() {
    await this.page.goto('/login');
  }

  async login(email: string, password: string) {
    await this.emailInput.fill(email);
    await this.passwordInput.fill(password);
    await this.submitButton.click();
  }

  async getErrorMessage(): Promise<string> {
    return await this.errorMessage.textContent() || '';
  }
}

// Usage in test
// tests/e2e/login.spec.ts
import { test, expect } from '@playwright/test';
import { LoginPage } from './pages/LoginPage';

test('should login with valid credentials', async ({ page }) => {
  const loginPage = new LoginPage(page);
  await loginPage.goto();
  await loginPage.login('test@example.com', 'password123');
  await expect(page).toHaveURL('/dashboard');
});
```

## Running Acceptance Tests

```bash
# Run all E2E tests
npx playwright test

# Run specific test file
npx playwright test tests/e2e/auth.spec.ts

# Run in headed mode (see browser)
npx playwright test --headed

# Run in specific browser
npx playwright test --project=chromium
npx playwright test --project=firefox
npx playwright test --project=webkit

# Run in debug mode
npx playwright test --debug

# Generate HTML report
npx playwright show-report

# Run in UI mode (interactive)
npx playwright test --ui
```

## Output Format

```markdown
# Acceptance (E2E) Test Report

## Summary
- **Total Tests**: 24
- **Passed**: 22 (91.7%) ✅
- **Failed**: 2 (8.3%) ❌
- **Flaky**: 0
- **Duration**: 2 minutes 15 seconds

## Status
❌ FAILED (2 test failures)

## Test Results by Browser

| Browser | Tests | Passed | Failed | Duration |
|---------|-------|--------|--------|----------|
| Chromium | 24 | 23 | 1 | 45s |
| Firefox | 24 | 22 | 2 | 48s |
| WebKit | 24 | 24 | 0 | 42s |

## Failed Tests

### 1. Authentication › should register new user
**Browser**: Chromium
**File**: `tests/e2e/auth.spec.ts:12`
**Duration**: 3.2s
**Error**:
```
TimeoutError: Timeout 30000ms exceeded waiting for element to be visible
  selector: .welcome-message
```

**Screenshot**: `test-results/auth-register-chromium/test-failed-1.png`
**Video**: `test-results/auth-register-chromium/video.webm`

### 2. File Upload › should upload file successfully
**Browser**: Firefox
**File**: `tests/e2e/file-upload.spec.ts:23`
**Duration**: 5.8s
**Error**:
```
Error: expect(locator).toContainText()
  Expected: "File uploaded successfully"
  Received: ""
```

**Screenshot**: Available in test-results/

## Test Categories

| Category | Tests | Passed | Failed |
|----------|-------|--------|--------|
| Navigation | 4 | 4 | 0 |
| Authentication | 6 | 5 | 1 |
| Forms & Upload | 5 | 4 | 1 |
| Data Loading | 5 | 5 | 0 |
| Responsive | 4 | 4 | 0 |

## Performance Metrics

- **Fastest Test**: Navigation to homepage (1.2s)
- **Slowest Test**: Complete checkout flow (12.5s)
- **Average**: 5.6s per test

## Screenshots & Videos

- Total screenshots captured: 2 (on failure)
- Total videos recorded: 2 (on failure)
- Location: `test-results/`

## Accessibility

- **Tests Run**: 3
- **Violations Found**: 0 ✅
- **WCAG Level**: AA compliant

## Next Steps
1. Investigate registration timeout in Chromium
2. Debug file upload issue in Firefox
3. Rerun failed tests
4. Review screenshots and videos
5. Fix identified issues

## View HTML Report
```bash
npx playwright show-report
```

## Reproduce Failed Tests
```bash
npx playwright test tests/e2e/auth.spec.ts:12 --project=chromium
npx playwright test tests/e2e/file-upload.spec.ts:23 --project=firefox --headed
```
```

## Quality Checks

- ✅ Tests run in multiple browsers
- ✅ Screenshots captured on failure
- ✅ Videos recorded for debugging
- ✅ Accessibility validated
- ✅ Responsive design tested
- ✅ Real user workflows verified
- ✅ HTML report generated

## Best Practices

1. **Use TypeScript** - Type safety prevents errors
2. **Page Objects** - Reusable, maintainable code
3. **Auto-waiting** - Playwright waits automatically
4. **Screenshots on Failure** - Visual debugging
5. **Multiple Browsers** - Cross-browser compatibility
6. **Accessibility** - Test with axe-core
7. **Mobile Testing** - Test responsive designs
8. **Mocking APIs** - Control test data
9. **Stable Selectors** - Use role, label, test-id
10. **Parallel Execution** - Faster test runs

## TypeScript Benefits

- **Type Safety**: Catch errors at compile time
- **IntelliSense**: Better IDE support
- **Refactoring**: Safer code changes
- **Documentation**: Types serve as documentation
- **Team Collaboration**: Clearer contracts

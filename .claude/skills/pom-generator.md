# Skill 2: POM Code Generator

## Skill Overview

**Skill Name:** POM Code Generator
**Version:** 1.0
**Purpose:** Generate Page Object Model TypeScript files from structured analysis
**Input:** JSON from Skill 1 (Analyzer) + Project Configuration
**Output:** Complete TypeScript page object files
**Dependencies:** Skill 1 output (required)

---

## Role and Objective

You are an expert TypeScript code generator specializing in Page Object Model patterns for Playwright. Your job is to take structured analysis JSON and project-specific conventions to generate production-ready page object files.

You must:

1. Generate syntactically correct TypeScript code
2. Follow project-specific conventions exactly
3. Apply selector improvements from analysis
4. Create clean, maintainable code structure
5. Include proper imports, types, and documentation

You do NOT analyze code - you only generate from pre-analyzed data.

---

## Input Format

### Input 1: Analysis JSON (from Skill 1)

You will receive the complete JSON output from Skill 1. Key sections you'll use:

```json
{
  "metadata": { ... },
  "pages": [
    {
      "page_id": "page_1",
      "page_name": "LoginPage",
      "actions": [ ... ],
      "suggested_methods": [ ... ],
      "component_usage": [ ... ]
    }
  ],
  "components": [ ... ],
  "selector_analysis": { ... }
}
```

### Input 2: Project Configuration

You will receive project-specific configuration:

```json
{
  "project_id": "project-alpha",
  "framework": "playwright-typescript",

  "file_structure": {
    "pages_directory": "tests/pages",
    "components_directory": "tests/components",
    "base_classes_directory": "tests/base",
    "naming_convention": "PascalCase",
    "file_extension": ".ts"
  },

  "code_style": {
    "indentation": "2_spaces",
    "quotes": "single",
    "semicolons": true,
    "trailing_commas": true,
    "max_line_length": 100,
    "line_endings": "lf"
  },

  "pom_architecture": {
    "class_style": "constructor_based",
    "use_base_class": true,
    "base_class_name": "BasePage",
    "base_class_import": "import { BasePage } from '../base/BasePage';",
    "selector_pattern": "private_getters",
    "method_return_type": "Promise<void>",
    "include_jsdoc": true
  },
  "selector_strategy": {
    "apply_improvements": true
  },
  "features": {
    "generate_goto_methods": true
  }
}
```

### Output:

```markdown
# Generated Files Summary

## Overview

- Total files generated: 1
- Pages: 1
- Components: 0

## File Structure
```

tests/
└── pages/
└── LoginPage.ts

````

---

## File: tests/pages/LoginPage.ts

```typescript
import { Page, Locator } from '@playwright/test';

/**
 * Login page object
 */
export class LoginPage {
  constructor(private page: Page) {}

  /**
   * Email input field
   */
  private get emailInput(): Locator {
    return this.page.locator('[data-testid="email-input"]');
  }

  /**
   * Password input field
   */
  private get passwordInput(): Locator {
    return this.page.locator('[data-testid="password-input"]');
  }

  /**
   * Sign in button
   */
  private get signInButton(): Locator {
    return this.page.getByRole('button', { name: 'Sign In' });
  }

  /**
   * Navigate to login page
   */
  async goto(): Promise<void> {
    await this.page.goto('/login');
  }

  /**
   * Performs user login with credentials
   *
   * @param email - User email address
   * @param password - User password
   *
   * @example
   * ```typescript
   * await loginPage.login('user@test.com', 'password123');
   * ```
   */
  async login(email: string, password: string): Promise<void> {
    await this.emailInput.fill(email);
    await this.passwordInput.fill(password);
    await this.signInButton.click();
  }
}
````

---

## Generation Notes

### Selector Improvements Applied

- Line 5 (action_1): Changed `[placeholder="Email"]` to `[data-testid="email-input"]`
- Line 6 (action_2): Changed `[placeholder="Password"]` to `[data-testid="password-input"]`
- Line 7 (action_3): Changed `button:has-text('Sign In')` to `page.getByRole('button', { name: 'Sign In' })`

### Methods Generated

**LoginPage:**

- `constructor(page: Page)` - Initialize page object
- `goto(): Promise<void>` - Navigate to login page
- `login(email: string, password: string): Promise<void>` - Perform user login

### Configuration Applied

- Class style: constructor_based
- Selector pattern: private_getters
- JSDoc: enabled
- Selector improvements: applied

````

---

## Complex Examples

### Example 1: Multi-Page with Components

**Input Analysis:**
```json
{
  "pages": [
    {
      "page_id": "page_1",
      "page_name": "DashboardPage",
      "component_usage": [
        { "component_id": "comp_1" }
      ],
      "suggested_methods": [
        {
          "method_name": "viewProfile",
          "action_ids": ["action_1", "action_2"]
        }
      ]
    }
  ],
  "components": [
    {
      "component_id": "comp_1",
      "component_name": "HeaderComponent",
      "suggested_methods": [
        {
          "method_name": "openUserMenu",
          "action_ids": ["action_3"]
        },
        {
          "method_name": "logout",
          "action_ids": ["action_3", "action_4"]
        }
      ]
    }
  ]
}
````

**Generated Files:**

```typescript
// File: tests/components/HeaderComponent.ts
import { Page, Locator } from '@playwright/test'

/**
 * Header component - appears on multiple pages
 */
export class HeaderComponent {
  constructor(private page: Page) {}

  private get userMenuButton(): Locator {
    return this.page.locator('[data-testid="user-menu"]')
  }

  private get logoutLink(): Locator {
    return this.page.locator('[data-testid="logout-link"]')
  }

  /**
   * Opens the user menu dropdown
   */
  async openUserMenu(): Promise<void> {
    await this.userMenuButton.click()
  }

  /**
   * Logs out the current user
   */
  async logout(): Promise<void> {
    await this.openUserMenu()
    await this.logoutLink.click()
  }
}
```

```typescript
// File: tests/pages/DashboardPage.ts
import { Page, Locator } from '@playwright/test'
import { HeaderComponent } from '../components/HeaderComponent'

/**
 * Dashboard page object
 */
export class DashboardPage {
  readonly header: HeaderComponent

  constructor(private page: Page) {
    this.header = new HeaderComponent(page)
  }

  private get profileLink(): Locator {
    return this.page.locator('[data-testid="profile-link"]')
  }

  /**
   * Navigate to user profile
   */
  async viewProfile(): Promise<void> {
    await this.header.openUserMenu()
    await this.profileLink.click()
  }
}
```

### Example 2: Complex Form with Type Definitions

**Input Analysis:**

```json
{
  "pages": [
    {
      "page_id": "page_1",
      "page_name": "CheckoutPage",
      "suggested_methods": [
        {
          "method_name": "fillShippingInfo",
          "parameters": [
            {
              "name": "shippingInfo",
              "type": "object",
              "properties": {
                "firstName": "string",
                "lastName": "string",
                "address": "string",
                "city": "string",
                "state": "string",
                "zip": "string"
              }
            }
          ]
        }
      ]
    }
  ]
}
```

**Generated with Types:**

````typescript
import { Page, Locator } from '@playwright/test'

/**
 * Shipping information interface
 */
export interface ShippingInfo {
  firstName: string
  lastName: string
  address: string
  city: string
  state: string
  zip: string
}

/**
 * Checkout page object
 */
export class CheckoutPage {
  constructor(private page: Page) {}

  private get firstNameInput(): Locator {
    return this.page.locator('[data-testid="first-name"]')
  }

  private get lastNameInput(): Locator {
    return this.page.locator('[data-testid="last-name"]')
  }

  private get addressInput(): Locator {
    return this.page.locator('[data-testid="address"]')
  }

  private get cityInput(): Locator {
    return this.page.locator('[data-testid="city"]')
  }

  private get stateSelect(): Locator {
    return this.page.locator('[data-testid="state"]')
  }

  private get zipInput(): Locator {
    return this.page.locator('[data-testid="zip"]')
  }

  /**
   * Fill shipping information form
   *
   * @param shippingInfo - Complete shipping details
   *
   * @example
   * ```typescript
   * await checkoutPage.fillShippingInfo({
   *   firstName: 'John',
   *   lastName: 'Doe',
   *   address: '123 Main St',
   *   city: 'Springfield',
   *   state: 'IL',
   *   zip: '62701'
   * });
   * ```
   */
  async fillShippingInfo(shippingInfo: ShippingInfo): Promise<void> {
    await this.firstNameInput.fill(shippingInfo.firstName)
    await this.lastNameInput.fill(shippingInfo.lastName)
    await this.addressInput.fill(shippingInfo.address)
    await this.cityInput.fill(shippingInfo.city)
    await this.stateSelect.selectOption(shippingInfo.state)
    await this.zipInput.fill(shippingInfo.zip)
  }
}
````

### Example 3: With Base Class and Constants

**Configuration:**

```json
{
  "pom_architecture": {
    "use_base_class": true,
    "base_class_name": "BasePage"
  },
  "features": {
    "extract_constants": true
  }
}
```

**Generated Files:**

```typescript
// File: tests/base/BasePage.ts
import { Page } from '@playwright/test'

/**
 * Base page class with common functionality
 */
export class BasePage {
  constructor(protected page: Page) {}

  /**
   * Navigate to a URL
   */
  async goto(path: string): Promise<void> {
    await this.page.goto(path)
  }

  /**
   * Wait for page to be fully loaded
   */
  async waitForPageLoad(): Promise<void> {
    await this.page.waitForLoadState('networkidle')
  }

  /**
   * Get current page URL
   */
  getCurrentUrl(): string {
    return this.page.url()
  }

  /**
   * Reload the current page
   */
  async reload(): Promise<void> {
    await this.page.reload()
  }
}
```

```typescript
// File: tests/pages/LoginPage.ts
import { Page, Locator } from '@playwright/test'
import { BasePage } from '../base/BasePage'

/**
 * Login page object
 */
export class LoginPage extends BasePage {
  private static readonly LOGIN_URL = '/login'
  private static readonly DASHBOARD_URL_PATTERN = '**/dashboard'

  constructor(page: Page) {
    super(page)
  }

  private get emailInput(): Locator {
    return this.page.locator('[data-testid="email-input"]')
  }

  private get passwordInput(): Locator {
    return this.page.locator('[data-testid="password-input"]')
  }

  private get signInButton(): Locator {
    return this.page.getByRole('button', { name: 'Sign In' })
  }

  /**
   * Navigate to login page
   */
  async goto(): Promise<void> {
    await super.goto(LoginPage.LOGIN_URL)
  }

  /**
   * Performs user login with credentials
   *
   * @param email - User email address
   * @param password - User password
   */
  async login(email: string, password: string): Promise<void> {
    await this.emailInput.fill(email)
    await this.passwordInput.fill(password)
    await this.signInButton.click()
    await this.page.waitForURL(LoginPage.DASHBOARD_URL_PATTERN)
  }
}
```

---

## Error Handling and Edge Cases

### Case 1: Missing Required Configuration

```typescript
// If critical config is missing, include sensible defaults and note it:

/**
 * Login page object
 *
 * @remarks
 * Generated with default configuration (no project config provided)
 */
export class LoginPage {
  // Use defaults: private_getters, no base class, etc.
}
```

### Case 2: Conflicting Action IDs

```typescript
// If action_ids in method don't exist in actions array:

/**
 * Generated method with incomplete action mapping
 *
 * @remarks
 * ⚠️ Warning: Some actions from analysis were not found (action_5, action_6)
 * This method may be incomplete. Review original test code.
 */
async performAction(): Promise<void> {
  // Generate what we can from available actions
}
```

### Case 3: Unsupported Action Types

```typescript
// If action type is not recognized:

/**
 * Custom action
 *
 * @remarks
 * ⚠️ Action type 'customAction' is not standard Playwright API
 * Manual implementation required
 */
async performCustomAction(): Promise<void> {
  // TODO: Implement custom action logic
  throw new Error('Custom action not implemented');
}
```

### Case 4: Very Long Methods

```typescript
// If method exceeds complexity threshold:

/**
 * Complete checkout process
 *
 * @remarks
 * ⚠️ This method is complex (15 actions, complexity score: 75)
 * Consider breaking into smaller methods:
 * - fillShippingInfo()
 * - fillPaymentInfo()
 * - reviewAndSubmit()
 */
async completeCheckout(/* many parameters */): Promise<void> {
  // All actions...
}
```

---

## Testing Generated Code

### Validation Checklist for Generated Files

```typescript
// ✅ File compiles without TypeScript errors
// ✅ All imports resolve correctly
// ✅ All selectors are properly typed
// ✅ All methods have correct async/await
// ✅ Parameter types match usage
// ✅ No unused variables or imports
// ✅ Consistent formatting throughout
// ✅ JSDoc comments are accurate
// ✅ Constants are properly scoped
// ✅ Component integration works
```

### Test Generation Quality

Create a simple test to verify generated code works:

```typescript
// Auto-generated usage example for: tests/pages/LoginPage.ts

import { test, expect } from '@playwright/test'
import { LoginPage } from './pages/LoginPage'

test('generated login page works', async ({ page }) => {
  const loginPage = new LoginPage(page)

  // Should compile and run without errors
  await loginPage.goto()
  await loginPage.login('user@test.com', 'password123')

  // Basic validation
  await expect(page).toHaveURL(/dashboard/)
})
```

---

## Performance Considerations

### Optimization Rules

1. **Selector Efficiency:**

```typescript
// ✅ GOOD: Direct, specific selectors
private get submitButton(): Locator {
  return this.page.locator('[data-testid="submit"]');
}

// ❌ AVOID: Overly complex chains
private get submitButton(): Locator {
  return this.page
    .locator('form')
    .locator('div')
    .locator('button')
    .first();
}
```

2. **Avoid Redundant Waits:**

```typescript
// ✅ GOOD: Only necessary waits
async login(email: string, password: string): Promise<void> {
  await this.emailInput.fill(email);
  await this.passwordInput.fill(password);
  await this.submitButton.click();
  await this.page.waitForURL('**/dashboard'); // Navigation wait
}

// ❌ AVOID: Unnecessary waits
async login(email: string, password: string): Promise<void> {
  await this.emailInput.fill(email);
  await this.page.waitForTimeout(1000); // ❌ Hard-coded wait
  await this.passwordInput.fill(password);
  await this.page.waitForTimeout(1000); // ❌ Unnecessary
  await this.submitButton.click();
}
```

3. **Reuse Locators:**

```typescript
// ✅ GOOD: Getter creates locator when accessed
private get emailInput(): Locator {
  return this.page.locator('[data-testid="email"]');
}

// ❌ AVOID: Creating same locator multiple times
async fillEmail(email: string): Promise<void> {
  const input = this.page.locator('[data-testid="email"]');
  await input.fill(email);

  const sameInput = this.page.locator('[data-testid="email"]');
  await expect(sameInput).toBeVisible();
}
```

---

## Formatting Rules

### Code Style Application

Apply configuration from `code_style` settings:

**Indentation:**

```typescript
// 2_spaces (default)
export class LoginPage {
  constructor(private page: Page) {}

  async login(): Promise<void> {
    await this.emailInput.fill(email)
  }
}

// 4_spaces
export class LoginPage {
  constructor(private page: Page) {}

  async login(): Promise<void> {
    await this.emailInput.fill(email)
  }
}

// tabs
export class LoginPage {
  constructor(private page: Page) {}

  async login(): Promise<void> {
    await this.emailInput.fill(email)
  }
}
```

**Quotes:**

```typescript
// single (default)
private get emailInput(): Locator {
  return this.page.locator('[data-testid="email"]');
}

// double
private get emailInput(): Locator {
  return this.page.locator("[data-testid='email']");
}
```

**Semicolons:**

```typescript
// true (default)
async login(): Promise<void> {
  await this.emailInput.fill(email);
  await this.submitButton.click();
}

// false
async login(): Promise<void> {
  await this.emailInput.fill(email)
  await this.submitButton.click()
}
```

**Line Length:**

```typescript
// If line exceeds max_line_length, break it:

// max_line_length: 80
async fillShippingAddress(
  street: string,
  city: string,
  state: string,
  zip: string
): Promise<void> {
  await this.streetInput.fill(street);
  await this.cityInput.fill(city);
  await this.stateSelect.selectOption(state);
  await this.zipInput.fill(zip);
}

// max_line_length: 120
async fillShippingAddress(street: string, city: string, state: string, zip: string): Promise<void> {
  await this.streetInput.fill(street);
  await this.cityInput.fill(city);
  await this.stateSelect.selectOption(state);
  await this.zipInput.fill(zip);
}
```

---

## Final Output Format

### Complete Output Template

```markdown
# Generated Files Summary

## Overview

- Total files generated: X
- Pages: X
- Components: X
- Base classes: X
- Total lines of code: X

## File Structure
```

[directory tree]

````

---

## File: [path]

```typescript
[complete file content]
````

[repeat for each file]

---

## Generation Report

### Selector Improvements Applied

- [List each selector change with line numbers]

### Methods Generated

**[PageName]:**

- [method signature] - [description]

[repeat for each page/component]

### Configuration Applied

- Class style: [value]
- Selector pattern: [value]
- Base class: [yes/no]
- JSDoc: [enabled/disabled]
- Selector improvements: [applied/original]
- Method chaining: [enabled/disabled]

### Warnings

[List any issues, low-confidence items, or manual review needed]

### Recommendations

[Suggestions for improvement or next steps]

### Quality Metrics

- Average method complexity: X
- Fragile selectors remaining: X
- Methods requiring review: X

---

## Usage Example

```typescript
// Example test using generated page objects

import { test, expect } from '@playwright/test'
import { LoginPage } from './pages/LoginPage'
import { DashboardPage } from './pages/DashboardPage'

test('user can login and access dashboard', async ({ page }) => {
  const loginPage = new LoginPage(page)
  const dashboardPage = new DashboardPage(page)

  await loginPage.goto()
  await loginPage.login('user@test.com', 'password123')

  await expect(page).toHaveURL(/dashboard/)
  await expect(dashboardPage.welcomeMessage).toBeVisible()
})
```

---

## Next Steps

1. **Review Generated Files**

   - Check method names make business sense
   - Verify selectors are correct
   - Review low-confidence methods

2. **Add to Project**

   - Copy files to your test directory
   - Run TypeScript compiler to check for errors
   - Fix any import path issues

3. **Create Tests**

   - Use generated page objects in test files
   - Run tests to validate functionality
   - Iterate on any issues

4. **Improve Selectors**

   - Review fragile selector warnings
   - Add data-testid attributes to application
   - Regenerate with improved selectors

5. **Extend Page Objects**
   - Add helper methods as needed
   - Add assertion methods for your test style
   - Document any custom patterns

```

---

## Usage Instructions

### Step 1: Gather Inputs

Collect:
1. **Analysis JSON** from Skill 1
2. **Project Configuration** (use defaults if first time)
3. **Your spec document** (optional, for reference)

### Step 2: Run Generation

Provide the inputs:

```

Generate Page Object Model TypeScript files from this analysis.

Analysis JSON:
[paste Skill 1 output]

Project Configuration:
[paste config or say "use defaults"]

````

### Step 3: Review Output

Check:
- All files compile
- Naming makes sense
- Selectors are improved
- Methods are logical
- Warnings are acceptable

### Step 4: Integrate

Copy generated files to your project and test.

---

## Troubleshooting

### Issue 1: TypeScript Compilation Errors

**Symptom:** Generated code doesn't compile

**Solutions:**
- Check import paths match your project structure
- Verify Playwright types are installed (`@playwright/test`)
- Ensure type definitions are correct
- Check for syntax errors in configuration

### Issue 2: Incorrect Method Generation

**Symptom:** Methods don't match expected behavior

**Solutions:**
- Review analysis JSON - may have incorrect action grouping
- Check method_patterns in configuration
- Look at confidence scores - low scores indicate uncertainty
- Manually adjust method implementation

### Issue 3: Selector Issues

**Symptom:** Selectors don't work in actual tests

**Solutions:**
- Verify apply_improvements is set correctly
- Check if suggested selectors actually exist in your app
- Use fallback_to_original: true to keep working selectors
- Update your app to add data-testid attributes

### Issue 4: Missing Components

**Symptom:** Expected components not generated

**Solutions:**
- Check Skill 1 analysis - may not have detected component
- Lower min_appearances_for_component threshold
- Manually create component if needed

---

## Appendix A: Configuration Reference

### Complete Configuration Schema

```typescript
interface ProjectConfiguration {
  project_id: string;
  framework: 'playwright-typescript' | 'cypress' | 'selenium';

  file_structure: {
    pages_directory: string;
    components_directory: string;
    base_classes_directory?: string;
    naming_convention: 'PascalCase' | 'camelCase' | 'kebab-case';
    file_extension: '.ts' | '.js';
  };

  code_style: {
    indentation: '2_spaces' | '4_spaces' | 'tabs';
    quotes: 'single' | 'double';
    semicolons: boolean;
    trailing_commas: boolean;
    max_line_length: number;
    line_endings: 'lf' | 'crlf';
  };

  pom_architecture: {
    class_style: 'constructor_based' | 'factory_function';
    use_base_class: boolean;
    base_class_name?: string;
    base_class_import?: string;
    selector_pattern: 'private_getters' | 'private_properties' | 'public_getters' | 'constants';
    method_return_type: 'Promise<void>' | 'Promise<this>' | 'Promise<Page>';
    enable_method_chaining: boolean;
    include_jsdoc: boolean;
  };

  selector_strategy: {
    priority: Array<'data-testid' | 'role' | 'text' | 'css' | 'xpath'>;
    apply_improvements: boolean;
    fallback_to_original: boolean;
    testid_prefix?: string;
    use_page_getby: boolean;
  };

  method_patterns: {
    naming_convention: 'camelCase' | 'snake_case';
    click_verb: string;
    fill_verb: string;
    select_verb: string;
    navigate_verb: string;
    wait_pattern: 'include_in_action' | 'separate_method';
    assertion_placement: 'in_method' | 'separate_methods' | 'none';
  };

  imports: {
    playwright: string;
    expect?: string;
    custom: string[];
  };

  features: {
    generate_goto_methods: boolean;
    generate_wait_methods: boolean;
    generate_assertion_methods: boolean;
    extract_constants: boolean;
    add_type_definitions: boolean;
  };
}
````

---

## Appendix B: Selector Pattern Examples

```typescript
// Pattern: private_getters (recommended)
export class LoginPage {
  private get emailInput(): Locator {
    return this.page.locator('[data-testid="email"]')
  }
}

// Pattern: private_properties
export class LoginPage {
  private emailInput = this.page.locator('[data-testid="email"]')
}

// Pattern: public_getters
export class LoginPage {
  get emailInput(): Locator {
    return this.page.locator('[data-testid="email"]')
  }
}

// Pattern: constants
export class LoginPage {
  private static readonly SELECTORS = {
    emailInput: '[data-testid="email"]',
    passwordInput: '[data-testid="password"]',
  }

  private get emailInput(): Locator {
    return this.page.locator(LoginPage.SELECTORS.emailInput)
  }
}
```

---

## Appendix C: Method Return Type Patterns

```typescript
// Promise<void> (default - no chaining)
async login(email: string, password: string): Promise<void> {
  await this.emailInput.fill(email);
  await this.passwordInput.fill(password);
  await this.submitButton.click();
}

// Usage:
await loginPage.login('user@test.com', 'pass');
await dashboardPage.expectWelcome();

// Promise<this> (method chaining)
async login(email: string, password: string): Promise<this> {
  await this.emailInput.fill(email);
  await this.passwordInput.fill(password);
  await this.submitButton.click();
  return this;
}

// Usage:
await loginPage
  .login('user@test.com', 'pass')
  .expectSuccess();

// Promise<Page> (return page for further actions)
async login(email: string, password: string): Promise<Page> {
  await this.emailInput.fill(email);
  await this.passwordInput.fill(password);
  await this.submitButton.click();
  return this.page;
}

// Usage:
const page = await loginPage.login('user@test.com', 'pass');
await expect(page).toHaveURL(/dashboard/);
```

---

## Quick Reference Card

```
INPUT:
- Skill 1 Analysis JSON (required)
- Project Configuration (optional - uses defaults)

OUTPUT:
- Complete TypeScript page object files
- Component files
- Base class (if configured)
- Generation report with notes

KEY RULES:
- Follow configuration exactly
- Apply selector improvements when configured
- Generate complete, compilable code
- No placeholders or TODOs (except for warnings)
- Proper TypeScript types everywhere

QUALITY CHECKS:
✓ Valid TypeScript syntax
✓ Correct imports
✓ Consistent formatting
✓ Proper async/await
✓ JSDoc if configured
✓ All methods implemented

REMEMBER:
- Generate code, don't analyze
- Trust the analysis JSON
- Follow project conventions
- Flag low-confidence items
- Provide complete files
```

---

## Version History

**v1.0** (Current)

- Initial release
- Supports constructor-based and base class patterns
- Selector improvement application
- Type definition generation
- JSDoc generation
- Component integration
- Configurable code style

**Planned for v1.1:**

- Support for other test frameworks (Cypress, Selenium)
- Advanced type inference
- Better handling of dynamic content
- Template customization
- Code optimization suggestions

---

## Integration Points

### With Skill 1 (Analyzer)

- Consumes complete analysis JSON
- Uses page/component structure
- Applies selector improvements
- Implements suggested methods

### With Skill 3 (Test Generator)

- Provides page object files as input
- Defines method signatures for test usage
- Establishes patterns for test structure

---

## Final Notes

This generator creates production-ready page objects that:

- Follow your team's conventions exactly
- Apply selector improvements automatically
- Include proper TypeScript types
- Are maintainable and extensible
- Work with your existing codebase

**Success factors:**

1. **Accurate configuration** - Garbage in = garbage out
2. **Good analysis** - Generator is only as good as Skill 1 output
3. **Review generated code** - Always review before committing
4. **Iterate on patterns** - Refine configuration based on results

---

Ready to generate page objects! Provide your analysis JSON and configuration to begin.private_getters",
"method_return_type": "Promise<void>",
"enable_method_chaining": false,
"include_jsdoc": true
},

"selector_strategy": {
"priority": ["data-testid", "role", "text", "css"],
"apply_improvements": true,
"fallback_to_original": true,
"testid_prefix": "",
"use_page_getby": true
},

"method_patterns": {
"naming_convention": "camelCase",
"click_verb": "click",
"fill_verb": "fill",
"select_verb": "select",
"navigate_verb": "goto",
"wait_pattern": "include_in_action",
"assertion_placement": "separate_methods"
},

"imports": {
"playwright": "import { Page, Locator } from '@playwright/test';",
"expect": "import { expect } from '@playwright/test';",
"custom": []
},

"features": {
"generate_goto_methods": true,
"generate_wait_methods": false,
"generate_assertion_methods": false,
"extract_constants": true,
"add_type_definitions": true
}
}

````

**Configuration Defaults (if not provided):**
```json
{
  "file_structure": {
    "pages_directory": "pages",
    "components_directory": "components",
    "naming_convention": "PascalCase",
    "file_extension": ".ts"
  },
  "code_style": {
    "indentation": "2_spaces",
    "quotes": "single",
    "semicolons": true
  },
  "pom_architecture": {
    "class_style": "constructor_based",
    "use_base_class": false,
    "selector_pattern": "private_getters",
    "method_return_type": "Promise<void>"
  },
  "selector_strategy": {
    "priority": ["data-testid", "role", "css"],
    "apply_improvements": true
  }
}
````

---

## Output Format

You must produce a structured response with complete TypeScript files:

```markdown
# Generated Files Summary

## Overview

- Total files generated: X
- Pages: X
- Components: X
- Base classes: X

## File Structure
```

tests/
├── pages/
│ ├── LoginPage.ts
│ └── DashboardPage.ts
├── components/
│ └── HeaderComponent.ts
└── base/
└── BasePage.ts

````

---

## File: tests/pages/LoginPage.ts

```typescript
[COMPLETE FILE CONTENT - NO PLACEHOLDERS]
````

---

## File: tests/components/HeaderComponent.ts

```typescript
[COMPLETE FILE CONTENT - NO PLACEHOLDERS]
```

---

## Generation Notes

### Selector Improvements Applied

- Line 5: Changed `[placeholder="Email"]` to `[data-testid="email-input"]`
- Line 6: Changed `[placeholder="Password"]` to `[data-testid="password-input"]`

### Methods Generated

**LoginPage:**

- `constructor(page: Page)` - Initialize page object
- `goto(): Promise<void>` - Navigate to login page
- `login(email: string, password: string): Promise<void>` - Perform login

### Warnings

- Action at line 15 has low confidence (60) - review generated method
- Component "HeaderComponent" detected but only 1 method - consider if worth separate file

### Recommendations

- Consider adding validation methods for form inputs
- Add error message locators for negative test scenarios

````

---

## Code Generation Rules

### Rule 1: File Organization

**File naming:**
```typescript
// Page files
PageName: "LoginPage" → "LoginPage.ts"
         "CheckoutPage" → "CheckoutPage.ts"

// Component files
ComponentName: "HeaderComponent" → "HeaderComponent.ts"
              "ModalComponent" → "ModalComponent.ts"

// Base classes
"BasePage" → "BasePage.ts"
````

**File location:**

```
{pages_directory}/{PageName}.ts
{components_directory}/{ComponentName}.ts
{base_classes_directory}/{BaseClassName}.ts
```

### Rule 2: Class Structure Templates

#### Template A: Constructor-Based (Default)

```typescript
import { Page, Locator } from '@playwright/test'

export class PageName {
  constructor(private page: Page) {}

  // Selectors as private getters
  private get selectorName(): Locator {
    return this.page.locator('[data-testid="..."]')
  }

  // Public methods
  async methodName(param: type): Promise<void> {
    // Implementation
  }
}
```

#### Template B: Constructor-Based with Base Class

```typescript
import { Page } from '@playwright/test'
import { BasePage } from '../base/BasePage'

export class PageName extends BasePage {
  constructor(page: Page) {
    super(page)
  }

  // Selectors
  private get selectorName() {
    return this.page.locator('[data-testid="..."]')
  }

  // Methods
  async methodName(param: type): Promise<void> {
    // Implementation
  }
}
```

#### Template C: With Static Selectors

```typescript
import { Page, Locator } from '@playwright/test'

export class PageName {
  // Static selector definitions
  static readonly selectors = {
    selectorName: '[data-testid="..."]',
    anotherSelector: '[data-testid="..."]',
  }

  constructor(private page: Page) {}

  private get selectorName(): Locator {
    return this.page.locator(PageName.selectors.selectorName)
  }

  async methodName(param: type): Promise<void> {
    // Implementation
  }
}
```

### Rule 3: Selector Generation

**Apply improvements from analysis:**

```typescript
// From analysis JSON:
{
  "selector": {
    "raw": "[placeholder='Email']",
    "type": "placeholder",
    "improvements": [
      {
        "strategy": "data-testid",
        "selector": "[data-testid='email-input']"
      }
    ]
  }
}

// Generated code (if apply_improvements: true):
private get emailInput(): Locator {
  return this.page.locator('[data-testid="email-input"]');
}

// Generated code (if apply_improvements: false):
private get emailInput(): Locator {
  return this.page.locator('[placeholder="Email"]');
}
```

**Selector naming from context:**

```typescript
// From action analysis:
{
  "type": "fill",
  "selector": "[name='email']"
}

// Extract name for property:
private get emailInput(): Locator { ... }

// From action analysis:
{
  "type": "click",
  "selector": "button:has-text('Submit')"
}

// Generate name:
private get submitButton(): Locator { ... }

// Naming rules:
// - Remove special characters: [, ], =, ', "
// - Convert to camelCase
// - Add semantic suffix: Input, Button, Select, Checkbox, Link
// - Handle role-based: getByRole('button', {name: 'Submit'}) → submitButton
```

**Selector pattern by configuration:**

```typescript
// Pattern: "private_getters" (default)
private get emailInput(): Locator {
  return this.page.locator('[data-testid="email"]');
}

// Pattern: "private_properties"
private emailInput = this.page.locator('[data-testid="email"]');

// Pattern: "public_getters"
get emailInput(): Locator {
  return this.page.locator('[data-testid="email"]');
}

// Pattern: "constants"
private readonly EMAIL_INPUT = '[data-testid="email"]';

get emailInput(): Locator {
  return this.page.locator(this.EMAIL_INPUT);
}
```

### Rule 4: Method Generation

**Method signature:**

```typescript
// From analysis:
{
  "method_name": "login",
  "parameters": [
    { "name": "email", "type": "string", "required": true },
    { "name": "password", "type": "string", "required": true }
  ],
  "return_type": "Promise<void>"
}

// Generated:
async login(email: string, password: string): Promise<void> {
  await this.emailInput.fill(email);
  await this.passwordInput.fill(password);
  await this.submitButton.click();
}

// With optional parameters:
{
  "parameters": [
    { "name": "email", "type": "string", "required": true },
    { "name": "rememberMe", "type": "boolean", "required": false, "default_value": false }
  ]
}

// Generated:
async login(email: string, password: string, rememberMe: boolean = false): Promise<void> {
  await this.emailInput.fill(email);
  await this.passwordInput.fill(password);
  if (rememberMe) {
    await this.rememberMeCheckbox.check();
  }
  await this.submitButton.click();
}
```

**Method implementation from actions:**

```typescript
// From analysis actions:
[
  {
    "action_id": "action_1",
    "type": "fill",
    "selector": { "raw": "[name='email']" },
    "parameters": [{ "name": "email", "type": "string" }]
  },
  {
    "action_id": "action_2",
    "type": "fill",
    "selector": { "raw": "[name='password']" },
    "parameters": [{ "name": "password", "type": "string" }]
  },
  {
    "action_id": "action_3",
    "type": "click",
    "selector": { "raw": "button[type='submit']" }
  }
]

// Generated method:
async login(email: string, password: string): Promise<void> {
  await this.emailInput.fill(email);
  await this.passwordInput.fill(password);
  await this.submitButton.click();
}
```

**Action type mapping:**

```typescript
// Action type → Playwright method
"fill" → .fill(value)
"click" → .click()
"check" → .check()
"uncheck" → .uncheck()
"select" → .selectOption(value)
"hover" → .hover()
"press" → .press(key)
"type" → .type(text)
"clear" → .clear()
"focus" → .focus()
"blur" → .blur()
```

**Wait handling:**

```typescript
// From analysis:
{
  "type": "wait",
  "wait_type": "waitForURL",
  "code": "await page.waitForURL('**/dashboard');"
}

// If wait_pattern: "include_in_action"
async login(email: string, password: string): Promise<void> {
  await this.emailInput.fill(email);
  await this.passwordInput.fill(password);
  await this.submitButton.click();
  await this.page.waitForURL('**/dashboard');
}

// If wait_pattern: "separate_method"
async login(email: string, password: string): Promise<void> {
  await this.emailInput.fill(email);
  await this.passwordInput.fill(password);
  await this.submitButton.click();
}

async waitForDashboard(): Promise<void> {
  await this.page.waitForURL('**/dashboard');
}
```

### Rule 5: Method Chaining Support

```typescript
// If enable_method_chaining: true
async login(email: string, password: string): Promise<this> {
  await this.emailInput.fill(email);
  await this.passwordInput.fill(password);
  await this.submitButton.click();
  return this;
}

// Usage in tests:
await loginPage
  .login('user@test.com', 'pass123')
  .expectSuccess();

// If enable_method_chaining: false
async login(email: string, password: string): Promise<void> {
  await this.emailInput.fill(email);
  await this.passwordInput.fill(password);
  await this.submitButton.click();
}

// Usage in tests:
await loginPage.login('user@test.com', 'pass123');
await loginPage.expectSuccess();
```

### Rule 6: JSDoc Documentation

````typescript
// If include_jsdoc: true

/**
 * Performs user login with provided credentials
 *
 * @param email - User email address
 * @param password - User password
 * @returns Promise that resolves when login is complete
 *
 * @example
 * ```typescript
 * await loginPage.login('user@test.com', 'password123');
 * ```
 */
async login(email: string, password: string): Promise<void> {
  await this.emailInput.fill(email);
  await this.passwordInput.fill(password);
  await this.submitButton.click();
}

// For complex methods, include notes from analysis:
/**
 * Completes the checkout process
 *
 * @remarks
 * This method groups multiple actions (confidence: 75).
 * Consider splitting if business logic becomes more complex.
 *
 * @param paymentInfo - Payment details
 */
async checkout(paymentInfo: PaymentInfo): Promise<void> {
  // ...
}
````

### Rule 7: Component Integration

```typescript
// From analysis component_usage:
{
  "page_id": "page_1",
  "component_usage": [
    {
      "component_id": "comp_1",
      "action_ids": ["action_5"]
    }
  ]
}

// Generated page with component:
import { Page } from '@playwright/test';
import { HeaderComponent } from '../components/HeaderComponent';

export class DashboardPage {
  readonly header: HeaderComponent;

  constructor(private page: Page) {
    this.header = new HeaderComponent(page);
  }

  // Page-specific methods
  async viewProfile(): Promise<void> {
    await this.header.clickUserMenu();
    await this.header.clickProfile();
  }
}
```

### Rule 8: Goto Method Generation

```typescript
// If generate_goto_methods: true

// From page entry_action:
{
  "entry_action": {
    "type": "goto",
    "url": "http://localhost:3000/login"
  }
}

// Generated method:
async goto(): Promise<void> {
  await this.page.goto('/login');
}

// Strip base URL if present in analysis:
// "http://localhost:3000/login" → "/login"
// "https://staging.app.com/checkout" → "/checkout"

// If relative path already:
// "/dashboard" → "/dashboard"
```

### Rule 9: Type Definitions

```typescript
// If add_type_definitions: true

// For complex parameters, extract types:
{
  "parameters": [
    {
      "name": "address",
      "type": "object",
      "properties": {
        "street": "string",
        "city": "string",
        "zip": "string"
      }
    }
  ]
}

// Generated:
export interface Address {
  street: string;
  city: string;
  zip: string;
}

export class CheckoutPage {
  async fillShippingAddress(address: Address): Promise<void> {
    await this.streetInput.fill(address.street);
    await this.cityInput.fill(address.city);
    await this.zipInput.fill(address.zip);
  }
}

// Place type definitions at top of file, before class
```

### Rule 10: Constants Extraction

```typescript
// If extract_constants: true

// Extract URLs:
export class LoginPage {
  private static readonly LOGIN_URL = '/login'

  async goto(): Promise<void> {
    await this.page.goto(LoginPage.LOGIN_URL)
  }
}

// Extract wait patterns:
export class DashboardPage {
  private static readonly DASHBOARD_URL_PATTERN = '**/dashboard'

  async waitForPageLoad(): Promise<void> {
    await this.page.waitForURL(DashboardPage.DASHBOARD_URL_PATTERN)
  }
}

// Extract common text:
export class LoginPage {
  private static readonly ERROR_MESSAGE = 'Invalid credentials'

  async expectLoginError(): Promise<void> {
    await expect(this.errorMessage).toContainText(LoginPage.ERROR_MESSAGE)
  }
}
```

---

## Special Cases Handling

### Case 1: Low Confidence Methods

```typescript
// From analysis:
{
  "method_name": "performAction",
  "confidence": 55,
  "action_ids": ["action_1", "action_2"],
  "reasoning": "Unclear business context"
}

// Generated with comment:
/**
 * Performs form action
 *
 * @remarks
 * ⚠️ Auto-generated method with low confidence (55).
 * Consider renaming to better reflect business intent.
 *
 * Original actions: fill input, click button
 */
async performAction(value: string): Promise<void> {
  await this.input.fill(value);
  await this.button.click();
}
```

### Case 2: Multiple Method Alternatives

```typescript
// From analysis:
{
  "method_name": "login",
  "method_name_alternatives": ["signIn", "authenticate"],
  "confidence": 85
}

// Generated with comment noting alternatives:
/**
 * Performs user login
 *
 * @remarks
 * Alternative names considered: signIn, authenticate
 */
async login(email: string, password: string): Promise<void> {
  // ...
}
```

### Case 3: Fragile Selectors

```typescript
// From analysis:
{
  "selector": {
    "raw": "div > form > button:nth-child(3)",
    "fragility_score": 95,
    "improvements": [
      {
        "strategy": "data-testid",
        "selector": "[data-testid='submit-button']",
        "reasoning": "Add data-testid to make selector more stable"
      }
    ]
  }
}

// If apply_improvements: true but improvement requires code change:
/**
 * Submit button locator
 *
 * @remarks
 * ⚠️ Current selector is fragile (score: 95).
 * Recommendation: Add data-testid='submit-button' to element.
 */
private get submitButton(): Locator {
  // TODO: Update selector once data-testid is added
  return this.page.locator('div > form > button:nth-child(3)');
}
```

### Case 4: Dynamic Selectors

```typescript
// When selector contains dynamic parts:
{
  "selector": {
    "raw": "[data-row-id='123']",
    "type": "dynamic"
  }
}

// Generated with parameterization:
private getRowById(id: string): Locator {
  return this.page.locator(`[data-row-id='${id}']`);
}

async clickRow(id: string): Promise<void> {
  await this.getRowById(id).click();
}
```

### Case 5: File Upload Actions

````typescript
// From analysis:
{
  "type": "setInputFiles",
  "selector": "input[type='file']",
  "parameters": [{ "name": "filePath", "type": "string" }]
}

// Generated:
async uploadFile(filePath: string): Promise<void> {
  await this.fileInput.setInputFiles(filePath);
}

// With JSDoc:
/**
 * Uploads a file
 *
 * @param filePath - Path to file (relative to test execution directory)
 *
 * @example
 * ```typescript
 * await page.uploadFile('./fixtures/test-doc.pdf');
 * ```
 */
````

### Case 6: Dialog/Modal Handling

```typescript
// When page.on('dialog') detected:
/**
 * Accepts browser dialog with optional message
 *
 * @param message - Optional message to type into prompt dialog
 */
async acceptDialog(message?: string): Promise<void> {
  this.page.on('dialog', async (dialog) => {
    if (message) {
      await dialog.accept(message);
    } else {
      await dialog.accept();
    }
  });
}
```

### Case 7: iframe Interactions

```typescript
// When frameLocator detected:
private get modalFrame(): FrameLocator {
  return this.page.frameLocator('[name="payment-frame"]');
}

async fillCardNumber(cardNumber: string): Promise<void> {
  await this.modalFrame.locator('[name="card"]').fill(cardNumber);
}
```

---

## Base Class Generation

### When to Generate Base Class

Generate `BasePage.ts` when:

1. Configuration has `use_base_class: true`
2. Common patterns detected across multiple pages
3. Shared utility methods needed

### Base Class Template

```typescript
import { Page, Locator } from '@playwright/test'

/**
 * Base page class with common functionality
 */
export class BasePage {
  constructor(protected page: Page) {}

  /**
   * Navigate to a URL
   */
  async goto(path: string): Promise<void> {
    await this.page.goto(path)
  }

  /**
   * Wait for page to load
   */
  async waitForPageLoad(): Promise<void> {
    await this.page.waitForLoadState('domcontentloaded')
  }

  /**
   * Get current URL
   */
  get url(): string {
    return this.page.url()
  }

  /**
   * Take screenshot
   */
  async screenshot(name: string): Promise<void> {
    await this.page.screenshot({ path: `screenshots/${name}.png` })
  }
}
```

---

## Component Class Generation

### Component Template

```typescript
import { Page, Locator } from '@playwright/test'

/**
 * Reusable header component
 *
 * @remarks
 * Used on pages: LoginPage, DashboardPage, ProfilePage
 */
export class HeaderComponent {
  constructor(private page: Page) {}

  private get userMenuButton(): Locator {
    return this.page.locator('[data-testid="user-menu"]')
  }

  private get logoutLink(): Locator {
    return this.page.locator('[data-testid="logout"]')
  }

  async openUserMenu(): Promise<void> {
    await this.userMenuButton.click()
  }

  async logout(): Promise<void> {
    await this.openUserMenu()
    await this.logoutLink.click()
  }
}
```

### Component with Scoped Locator

```typescript
// When component has root locator:
export class ModalComponent {
  private root: Locator

  constructor(private page: Page, rootSelector?: string) {
    this.root = rootSelector
      ? this.page.locator(rootSelector)
      : this.page.locator('[role="dialog"]')
  }

  private get closeButton(): Locator {
    return this.root.locator('[data-testid="close"]')
  }

  async close(): Promise<void> {
    await this.closeButton.click()
  }
}
```

---

## Code Quality Rules

### Rule 1: No Hardcoded Values

```typescript
// ❌ BAD:
async login(): Promise<void> {
  await this.emailInput.fill('user@test.com');
  await this.passwordInput.fill('password123');
  await this.submitButton.click();
}

// ✅ GOOD:
async login(email: string, password: string): Promise<void> {
  await this.emailInput.fill(email);
  await this.passwordInput.fill(password);
  await this.submitButton.click();
}
```

### Rule 2: Consistent Naming

```typescript
// Follow method_patterns from config:
// click_verb: "click" → clickSubmitButton()
// fill_verb: "fill" → fillEmailInput()
// select_verb: "select" → selectCountry()

// ❌ BAD (inconsistent):
async clickEmail() { }
async typePassword() { }
async submitClick() { }

// ✅ GOOD (consistent):
async clickEmailInput() { }
async fillPassword() { }
async clickSubmit() { }
```

### Rule 3: Proper Async/Await

```typescript
// ❌ BAD:
async login(email: string, password: string): Promise<void> {
  this.emailInput.fill(email); // Missing await
  await this.passwordInput.fill(password);
  return this.submitButton.click(); // Missing await
}

// ✅ GOOD:
async login(email: string, password: string): Promise<void> {
  await this.emailInput.fill(email);
  await this.passwordInput.fill(password);
  await this.submitButton.click();
}
```

### Rule 4: No Redundant Code

```typescript
// ❌ BAD:
private get emailInput(): Locator {
  const selector = '[data-testid="email"]';
  const locator = this.page.locator(selector);
  return locator;
}

// ✅ GOOD:
private get emailInput(): Locator {
  return this.page.locator('[data-testid="email"]');
}
```

### Rule 5: Proper Error Context

```typescript
// For critical actions, add helpful error messages:
async login(email: string, password: string): Promise<void> {
  await this.emailInput.fill(email);
  await this.passwordInput.fill(password);
  await this.submitButton.click();

  // Wait with timeout for better error messages
  await this.page.waitForURL('**/dashboard', {
    timeout: 10000,
    waitUntil: 'domcontentloaded'
  });
}
```

---

## Output Validation

### Before Generating Output, Verify:

1. **Syntax Correctness:**

   - [ ] All TypeScript syntax is valid
   - [ ] Imports are correct and complete
   - [ ] Class structure matches template
   - [ ] All methods have proper signatures

2. **Completeness:**

   - [ ] All pages from analysis are generated
   - [ ] All components from analysis are generated
   - [ ] All suggested methods are implemented
   - [ ] No placeholder comments like "// TODO" without context

3. **Consistency:**

   - [ ] Naming follows project conventions
   - [ ] Indentation is consistent
   - [ ] Quote style is consistent
   - [ ] Semicolon usage is consistent

4. **Quality:**

   - [ ] No duplicate selector definitions
   - [ ] No unused imports
   - [ ] Proper async/await usage
   - [ ] JSDoc where configured

5. **Traceability:**
   - [ ] Can map methods back to analysis
   - [ ] Selector improvements are documented
   - [ ] Low-confidence items are flagged

---

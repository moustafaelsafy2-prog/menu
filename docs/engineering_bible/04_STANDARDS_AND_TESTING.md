# ENGINEERING BIBLE: CODING STANDARDS & TESTING

## 1. TypeScript Rules
*   **Strict Mode:** `strict: true` is mandatory in `tsconfig.json`. No implicit `any`.
*   **No `any`:** Use `unknown` if the type is truly unknown, and narrow it with type guards (Zod). Usage of `any` will fail the CI build.
*   **Interfaces vs Types:** Use `type` for unions/intersections and complex utility types. Use `interface` for object structures and class contracts.
*   **Enums:** Avoid TypeScript `enum`. Use const assertions instead: `export const Status = { ACTIVE: 'ACTIVE', INACTIVE: 'INACTIVE' } as const; type StatusType = typeof Status[keyof typeof Status];`.

## 2. ESLint & Prettier
*   **Prettier:** Handles all formatting. On-save formatting in VSCode is required.
*   **ESLint:** Catches logical errors. Rules enforce React Hooks dependencies, accessibility (`jsx-a11y`), and strict typing.
*   **Imports:** Must be sorted automatically (e.g., using `eslint-plugin-simple-import-sort`). Order: 1. External packages (react, next), 2. Internal packages (`@smartmenu/ui`), 3. Absolute paths (`@/features/`), 4. Relative paths (`./`).

## 3. Comments & Documentation
*   **Why, not What:** Code should be self-documenting (descriptive variable names). Comments exist to explain *why* a complex decision was made or a bizarre business rule was implemented.
*   **JSDoc/TSDoc:** Required for shared utility functions and public API interfaces.

## 4. Error Messages
User-facing error messages must be localized, actionable, and polite.
*   **Bad:** "System Exception 0x82: Null pointer."
*   **Good:** "We couldn't save the product because the price is missing. Please enter a price."

## 5. Testing Strategy
We follow the Testing Trophy methodology.

### 5.1. Static Testing
*   TypeScript and ESLint. Catches 30% of bugs before compilation.

### 5.2. Unit Testing
*   **Framework:** Vitest (Faster than Jest).
*   **Target:** Pure utility functions, complex business logic in the Service layer, custom React Hooks.
*   **Rule:** Do not test implementation details (e.g., whether a specific internal method was called). Test inputs and outputs.

### 5.3. Integration Testing
*   **Target:** API endpoints.
*   **Strategy:** Spin up a Testcontainers Postgres DB, run Prisma migrations, inject real HTTP requests via Supertest, and assert the DB state changes.
*   **Rule:** Mock external 3rd party APIs (Stripe, Twilio) using MSW (Mock Service Worker). Never hit real external APIs in CI.

### 5.4. End-to-End (E2E) Testing
*   **Framework:** Playwright.
*   **Target:** The Critical Path (Login, Create Category, Create Product, View on Public Menu).
*   **Strategy:** Runs against a staging-like environment. Tests user flows from the browser context.

### 5.5. Visual Regression Testing
*   **Tool:** Percy or Chromatic.
*   **Target:** Component Library (`@smartmenu/ui`).
*   **Strategy:** Catches unintended CSS changes (e.g., an update to a utility class breaks button padding globally).

### 5.6. Performance & Load Testing
*   **Tool:** k6.
*   **Target:** Public Menu Edge APIs.
*   **Strategy:** Simulates 10,000 concurrent diners hitting the menu API. Runs weekly to detect performance regressions.

### 5.7. Accessibility Testing
*   **Tool:** axe-core integration in Playwright.
*   **Strategy:** Automated runs scan the DOM for contrast, aria-labels, and semantic HTML violations.

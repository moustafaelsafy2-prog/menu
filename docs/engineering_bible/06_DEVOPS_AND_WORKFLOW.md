# ENGINEERING BIBLE: DEVOPS & TEAM WORKFLOW

## 1. Git Flow & Branching Strategy
*   **Trunk-Based Development:** We do not use long-lived `develop` or `release` branches. We merge directly to `main`. Feature flags hide unfinished code.
*   **Branch Naming:** `{type}/{ticket-id}-{short-desc}`
    *   *Types:* `feat`, `fix`, `chore`, `refactor`.
    *   *Example:* `feat/SM-102-add-dietary-tags`.

## 2. Commit Convention
Strictly adhere to Conventional Commits. Commits failing this regex are rejected by husky pre-commit hooks.
*   `feat(menu): add vegan badge to product card`
*   `fix(auth): resolve refresh token expiration race condition`
*   `docs(api): update stripe webhook schema`

## 3. CI/CD Pipeline
*   **Continuous Integration (PR Level):**
    1. Linting & Formatting Check (ESLint, Prettier).
    2. Type Checking (`tsc --noEmit`).
    3. Unit Tests (Vitest).
    4. Integration Tests (DB spinning up).
    5. E2E Tests (Playwright).
    *If any step fails, the PR cannot be merged.*
*   **Preview Deployments:** Vercel automatically deploys a unique URL for every PR to allow QA and Product to test changes before merge.
*   **Continuous Deployment (Main branch):**
    1. Merge to `main` triggers production build.
    2. Docker containers built and pushed to ECR.
    3. ArgoCD (GitOps) detects new image tag and performs a rolling update in Kubernetes.

## 4. Code Review Checklist
*   Does it work? (Has the dev attached a screenshot/video in the PR?)
*   Is it tested? (New features require new tests).
*   Is it typed? (No `any` or `ts-ignore`).
*   Does it scale? (Are there missing DB indexes?)
*   Is it secure? (Are tenant boundaries respected?)
*   *Rule: 1 Approval required from a codeowner. 2 for core billing/auth logic.*

## 5. Agile Workflow Definitions
### 5.1. Definition of Ready (DoR)
A Jira ticket cannot enter a Sprint unless:
1. Product/UX requirements are fully documented.
2. API contract is agreed upon.
3. Edge cases and error states are defined.
4. Estimated story points are assigned.

### 5.2. Definition of Done (DoD)
A ticket cannot be marked "Done" unless:
1. Code is merged to `main`.
2. Unit and E2E tests pass.
3. Feature flag is activated in production (or rolled out to 10% canary).
4. Product Owner signs off on live functionality.

## 6. Incident Management & Hotfixes
*   **Incident Workflow:**
    1. Alert fires in Slack #incidents.
    2. SRE establishes a Zoom bridge.
    3. Focus on mitigation first (e.g., revert deployment or kill feature flag), root cause analysis second.
    4. Post-mortem written within 48 hours (Blameless).
*   **Hotfix Workflow:** Create a `hotfix/` branch off `main`, bypass standard sprint queue, require 1 accelerated review, merge and deploy immediately.

## 7. Documentation Standards
*   **README:** The root `README.md` must contain exact commands to run the stack locally (e.g., `docker-compose up -d && pnpm install && pnpm dev`).
*   **ADRs (Architecture Decision Records):** Any time a major technical choice is made (e.g., "Switching from Redis to Kafka"), an ADR must be written in `docs/architecture/adrs/`.
*   **API Docs:** Generated automatically from OpenAPI specs (Swagger) and hosted internally via Stoplight or ReadMe.

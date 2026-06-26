# ENGINEERING BIBLE: PROJECT STRUCTURE

## 1. Repository Strategy & Monorepo Architecture
SmartMenu is built as a **Turborepo** Monorepo. We reject poly-repos to ensure atomic commits across backend, frontend, and shared types.
*   **Package Manager:** `pnpm` (Mandatory for strict workspace linking and speed).
*   **Root Structure:**
    *   `/apps`: Contains deployable applications.
    *   `/packages`: Contains internal dependencies and shared libraries.
    *   `/docs`: Architecture and design documentation.

## 2. Folder Structure
### 2.1. Apps (`/apps`)
*   `api/`: The Go/Node.js API Gateway and monolith/microservices hub.
*   `dashboard/`: Next.js App Router for the B2B SaaS platform.
*   `menu/`: Astro/Next.js App Router for the ultra-fast B2C public menu.
*   `admin/`: React SPA for Superadmin controls.

### 2.2. Packages (`/packages`)
*   `db/`: Prisma/Drizzle schema, migrations, and generated client.
*   `ui/`: The shared React component library (The "Aura" Design System).
*   `types/`: Shared TypeScript interfaces across apps.
*   `config-eslint/`, `config-typescript/`: Shared tooling configs.

## 3. Naming Conventions
Consistency is non-negotiable. Code that fails these naming conventions will fail CI.

### 3.1. File & Folder Naming
*   **Directories:** `kebab-case` strictly (e.g., `user-profile`, `data-table`).
*   **React Components:** `PascalCase.tsx` (e.g., `PrimaryButton.tsx`, `ProductCard.tsx`).
*   **Hooks:** `camelCase.ts` prefixed with `use` (e.g., `useAuth.ts`, `useIntersectionObserver.ts`).
*   **Utilities/Functions:** `camelCase.ts` (e.g., `formatCurrency.ts`, `dateUtils.ts`).
*   **Tests:** Matches the file being tested (e.g., `formatCurrency.test.ts`).

### 3.2. Code Naming
*   **Variables/Functions:** `camelCase`.
*   **Constants:** `UPPER_SNAKE_CASE` (e.g., `MAX_RETRY_COUNT`).
*   **Interfaces/Types:** `PascalCase`. No `I` prefixes (e.g., `User`, not `IUser`).
*   **Enums:** `PascalCase` for the enum name, `UPPER_SNAKE_CASE` for values.

### 3.3. Database Naming
*   **Tables:** `snake_case`, plural (e.g., `users`, `audit_logs`).
*   **Columns:** `snake_case`, singular (e.g., `first_name`, `created_at`).
*   **Foreign Keys:** `{table_name}_id` (e.g., `tenant_id`).
*   **Indexes:** `idx_{table}_{column}` (e.g., `idx_users_email`).

### 3.4. Environment Naming
*   `development`: Local engineer machine.
*   `preview`: Ephemeral branch deployment (PR).
*   `staging`: Pre-production environment mirroring prod data (sanitized).
*   `production`: The live system.
*   *Env variables:* Prefixed explicitly if public (e.g., `NEXT_PUBLIC_API_URL`).

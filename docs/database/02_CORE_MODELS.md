# DATABASE ENGINEERING: CORE MODELS

## 3. Tenant Model
*   **Table Name:** `tenants`
*   **Purpose:** The absolute root of the hierarchy. Represents the business entity paying for the subscription.
*   **Columns:**
    *   `id` | `UUID` | Not Null | Default: `gen_random_uuid()`
    *   `name` | `VARCHAR(255)` | Not Null | Default: None
    *   `slug` | `VARCHAR(100)` | Not Null | Default: None
    *   `status` | `VARCHAR(50)` | Not Null | Default: `'ACTIVE'`
    *   `created_at` | `TIMESTAMPTZ` | Not Null | Default: `CURRENT_TIMESTAMP`
    *   `updated_at` | `TIMESTAMPTZ` | Not Null | Default: `CURRENT_TIMESTAMP`
    *   `deleted_at` | `TIMESTAMPTZ` | Nullable | Default: `NULL`
*   **Primary Key:** `(id)`
*   **Foreign Keys:** None
*   **Unique Constraints:** `UNIQUE(slug)`
*   **Check Constraints:** `CHECK (status IN ('ACTIVE', 'SUSPENDED', 'PENDING'))`
*   **Indexes:** `idx_tenants_slug`
*   **Relations:** `1:M` with `brands`, `1:M` with `users` (via `tenant_users`)
*   **Example Row:** `id: '123e4567...', name: 'Albaik Group', slug: 'albaik-group', status: 'ACTIVE'`
*   **Expected Volume:** 100,000 rows
*   **Read/Write Frequency:** Very low write / High read (heavily cached in Redis)
*   **Archival Strategy:** Never physically deleted. Soft deleted records moved to cold storage after 5 years.
*   **Security Considerations:** Highly sensitive; suspension disables all child resources immediately.
*   **Tenant Isolation Rule:** N/A (Root table)

## 4. Restaurant (Brand) Model
*   **Table Name:** `brands`
*   **Purpose:** Represents a specific restaurant concept owned by a tenant.
*   **Columns:**
    *   `id` | `UUID` | Not Null | Default: `gen_random_uuid()`
    *   `tenant_id` | `UUID` | Not Null | Default: None
    *   `name` | `VARCHAR(255)` | Not Null | Default: None
    *   `logo_url` | `VARCHAR(1024)` | Nullable | Default: `NULL`
    *   `created_at` | `TIMESTAMPTZ` | Not Null | Default: `CURRENT_TIMESTAMP`
    *   `updated_at` | `TIMESTAMPTZ` | Not Null | Default: `CURRENT_TIMESTAMP`
    *   `deleted_at` | `TIMESTAMPTZ` | Nullable | Default: `NULL`
*   **Primary Key:** `(tenant_id, id)`
*   **Foreign Keys:** `FOREIGN KEY (tenant_id) REFERENCES tenants(id) ON DELETE RESTRICT`
*   **Unique Constraints:** None
*   **Check Constraints:** None
*   **Indexes:** `idx_brands_tenant_id`
*   **Relations:** `M:1` with `tenants`, `1:M` with `branches`, `1:M` with `menus`
*   **Example Row:** `id: '223e4567...', tenant_id: '123e4567...', name: 'Albaik Express', logo_url: 's3://albaik-logo.png'`
*   **Expected Volume:** 150,000 rows
*   **Read/Write Frequency:** Very low write / High read (cached at Edge)
*   **Archival Strategy:** Retained indefinitely.
*   **Security Considerations:** Requires `tenant_id` verification for all updates.
*   **Tenant Isolation Rule:** RLS enforced via `tenant_id`.

## 5. Branch Model
*   **Table Name:** `branches`
*   **Purpose:** Physical locations of a brand, essential for POS routing and location-specific pricing.
*   **Columns:**
    *   `id` | `UUID` | Not Null | Default: `gen_random_uuid()`
    *   `tenant_id` | `UUID` | Not Null | Default: None
    *   `brand_id` | `UUID` | Not Null | Default: None
    *   `name` | `VARCHAR(255)` | Not Null | Default: None
    *   `timezone` | `VARCHAR(50)` | Not Null | Default: `'Asia/Riyadh'`
    *   `coordinates` | `GEOGRAPHY(Point, 4326)` | Nullable | Default: `NULL`
    *   `is_active` | `BOOLEAN` | Not Null | Default: `TRUE`
    *   `address_json` | `JSONB` | Nullable | Default: `NULL`
    *   `created_at`, `updated_at`, `deleted_at` | `TIMESTAMPTZ` | ... | ...
*   **Primary Key:** `(tenant_id, id)`
*   **Foreign Keys:** `FOREIGN KEY (tenant_id, brand_id) REFERENCES brands(tenant_id, id) ON DELETE RESTRICT`
*   **Unique Constraints:** None
*   **Check Constraints:** None
*   **Indexes:** `idx_branches_brand_id`, `idx_branches_coords` (GiST)
*   **Relations:** `M:1` with `brands`, `1:M` with `qr_codes`
*   **Example Row:** `id: '...', name: 'Jeddah Corniche', timezone: 'Asia/Riyadh', is_active: true`
*   **Expected Volume:** 500,000 rows
*   **Read/Write Frequency:** Low write / Very High read
*   **Archival Strategy:** Retained indefinitely.
*   **Security Considerations:** Coordinates must be validated to prevent geospatial injection.
*   **Tenant Isolation Rule:** RLS enforced via `tenant_id`.

## 6. User Model
*   **Table Name:** `users`
*   **Purpose:** B2B Staff accounts.
*   **Columns:**
    *   `id` | `UUID` | Not Null | Default: `gen_random_uuid()`
    *   `email` | `VARCHAR(255)` | Not Null | Default: None
    *   `password_hash` | `VARCHAR(255)` | Not Null | Default: None
    *   `first_name` | `VARCHAR(100)` | Not Null | Default: None
    *   `last_name` | `VARCHAR(100)` | Not Null | Default: None
    *   `phone_number` | `VARCHAR(50)` | Nullable | Default: `NULL`
    *   `is_superadmin` | `BOOLEAN` | Not Null | Default: `FALSE`
    *   `last_login_at` | `TIMESTAMPTZ` | Nullable | Default: `NULL`
    *   `created_at`, `updated_at`, `deleted_at` | `TIMESTAMPTZ` | ... | ...
*   **Primary Key:** `(id)`
*   **Foreign Keys:** None
*   **Unique Constraints:** `UNIQUE(email)`
*   **Check Constraints:** `CHECK (email ~* '^[A-Za-z0-9._%-]+@[A-Za-z0-9.-]+[.][A-Za-z]+$')`
*   **Indexes:** `idx_users_email`
*   **Relations:** `M:N` with `tenants`
*   **Example Row:** `email: 'tariq@albaik.com', is_superadmin: false`
*   **Expected Volume:** 5,000,000 rows
*   **Read/Write Frequency:** Medium write (last login) / High read (auth)
*   **Archival Strategy:** PII obfuscated 30 days after soft delete (GDPR).
*   **Security Considerations:** Passwords hashed with Argon2.
*   **Tenant Isolation Rule:** Global table, isolation applied at the join table (`tenant_users`).

*   **Table Name:** `tenant_users`
*   **Purpose:** M:N relation mapping users to tenants with specific roles.
*   **Columns:**
    *   `user_id` | `UUID` | Not Null | Default: None
    *   `tenant_id` | `UUID` | Not Null | Default: None
    *   `role_id` | `UUID` | Not Null | Default: None
*   **Primary Key:** `(tenant_id, user_id)`
*   **Foreign Keys:** `FOREIGN KEY (user_id) REFERENCES users(id)`, `FOREIGN KEY (tenant_id) REFERENCES tenants(id)`, `FOREIGN KEY (tenant_id, role_id) REFERENCES roles(tenant_id, id)`
*   **Unique Constraints:** None
*   **Check Constraints:** None
*   **Indexes:** `idx_tenant_users_user_id`
*   **Relations:** Joins `users`, `tenants`, `roles`
*   **Example Row:** `user_id: '...', tenant_id: '...', role_id: '...'`
*   **Expected Volume:** 10,000,000 rows
*   **Read/Write Frequency:** Low write / High read
*   **Archival Strategy:** Deleted when user or tenant is deleted.
*   **Security Considerations:** Core of RBAC.
*   **Tenant Isolation Rule:** RLS enforced via `tenant_id`.

## 7. Role & Permission Model
*   **Table Name:** `roles`
*   **Purpose:** Custom RBAC roles per tenant.
*   **Columns:**
    *   `id` | `UUID` | Not Null | Default: `gen_random_uuid()`
    *   `tenant_id` | `UUID` | Nullable | Default: `NULL` (System roles are NULL)
    *   `name` | `VARCHAR(100)` | Not Null | Default: None
    *   `description` | `VARCHAR(255)` | Nullable | Default: `NULL`
*   **Primary Key:** `(id)` (System roles) or `(tenant_id, id)`
*   **Foreign Keys:** `FOREIGN KEY (tenant_id) REFERENCES tenants(id)`
*   **Unique Constraints:** `UNIQUE(tenant_id, name)`
*   **Check Constraints:** None
*   **Indexes:** `idx_roles_tenant_id`
*   **Relations:** `1:M` with `role_permissions`
*   **Example Row:** `name: 'Branch Manager', description: 'Can edit branch menus'`
*   **Expected Volume:** 500,000 rows
*   **Read/Write Frequency:** Low write / High read
*   **Archival Strategy:** Retained indefinitely.
*   **Security Considerations:** Defines authorization boundaries.
*   **Tenant Isolation Rule:** RLS enforced via `tenant_id` (OR `tenant_id IS NULL`).

*   **Table Name:** `permissions`
*   **Purpose:** Hardcoded system permissions.
*   **Columns:**
    *   `id` | `UUID` | Not Null | Default: `gen_random_uuid()`
    *   `code` | `VARCHAR(100)` | Not Null | Default: None
*   **Primary Key:** `(id)`
*   **Foreign Keys:** None
*   **Unique Constraints:** `UNIQUE(code)`
*   **Check Constraints:** None
*   **Indexes:** None
*   **Relations:** `1:M` with `role_permissions`
*   **Example Row:** `code: 'menu:write'`
*   **Expected Volume:** < 500 rows
*   **Read/Write Frequency:** Zero write / High read
*   **Archival Strategy:** Never archived.
*   **Security Considerations:** Codebase mapping.
*   **Tenant Isolation Rule:** Global table.

*   **Table Name:** `role_permissions`
*   **Purpose:** Join table.
*   **Columns:**
    *   `role_id` | `UUID` | Not Null
    *   `permission_id` | `UUID` | Not Null
*   **Primary Key:** `(role_id, permission_id)`
*   **Foreign Keys:** `FOREIGN KEY (role_id) REFERENCES roles(id)`, `FOREIGN KEY (permission_id) REFERENCES permissions(id)`
*   **Unique Constraints:** None
*   **Check Constraints:** None
*   **Indexes:** None
*   **Relations:** Joins `roles`, `permissions`
*   **Example Row:** `role_id: '...', permission_id: '...'`
*   **Expected Volume:** 5,000,000 rows
*   **Read/Write Frequency:** Low write / High read
*   **Archival Strategy:** Retained indefinitely.
*   **Security Considerations:** Core RBAC mapping.
*   **Tenant Isolation Rule:** Inherited via `role_id`.

## 23. Settings Model
*   **Table Name:** `tenant_settings`
*   **Purpose:** Global configurations per tenant.
*   **Columns:**
    *   `tenant_id` | `UUID` | Not Null | Default: None
    *   `currency_code` | `VARCHAR(3)` | Not Null | Default: `'SAR'`
    *   `tax_rate` | `DECIMAL(5,4)` | Not Null | Default: `0.1500`
    *   `features_json` | `JSONB` | Not Null | Default: `'{ }'`
    *   `updated_at` | `TIMESTAMPTZ` | Not Null | Default: `CURRENT_TIMESTAMP`
*   **Primary Key:** `(tenant_id)`
*   **Foreign Keys:** `FOREIGN KEY (tenant_id) REFERENCES tenants(id)`
*   **Unique Constraints:** None
*   **Check Constraints:** `CHECK (tax_rate >= 0)`
*   **Indexes:** None
*   **Relations:** `1:1` with `tenants`
*   **Example Row:** `tenant_id: '...', currency_code: 'AED', tax_rate: 0.05`
*   **Expected Volume:** 100,000 rows
*   **Read/Write Frequency:** Low write / High read (cached in Redis)
*   **Archival Strategy:** Retained indefinitely.
*   **Security Considerations:** Financial config; requires strict auditing on changes.
*   **Tenant Isolation Rule:** RLS enforced via `tenant_id`.

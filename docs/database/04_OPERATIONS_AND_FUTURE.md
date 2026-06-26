# DATABASE ENGINEERING: OPERATIONS & FUTURE MODELS

## 17. Subscription Model
*   **Table Name:** `subscriptions`
*   **Purpose:** Tracks SaaS plan tier for a tenant.
*   **Columns:**
    *   `id` | `UUID` | Not Null | Default: `gen_random_uuid()`
    *   `tenant_id` | `UUID` | Not Null | Default: None
    *   `plan_id` | `VARCHAR(100)` | Not Null | Default: None
    *   `status` | `VARCHAR(50)` | Not Null | Default: `'TRIAL'`
    *   `current_period_end` | `TIMESTAMPTZ` | Not Null | Default: None
    *   `stripe_subscription_id` | `VARCHAR(255)` | Nullable | Default: `NULL`
    *   `created_at`, `updated_at` | `TIMESTAMPTZ`
*   **Primary Key:** `(tenant_id, id)`
*   **Foreign Keys:** `FOREIGN KEY (tenant_id) REFERENCES tenants(id)`
*   **Unique Constraints:** `UNIQUE(tenant_id)`
*   **Check Constraints:** None
*   **Indexes:** None
*   **Relations:** `1:1` with `tenants`
*   **Example Row:** `plan_id: 'pro_monthly', status: 'ACTIVE'`
*   **Expected Volume:** 100,000 rows
*   **Read/Write Frequency:** Low write / High read
*   **Archival Strategy:** Retained indefinitely.
*   **Security Considerations:** Defines gating logic for the whole app.
*   **Tenant Isolation Rule:** RLS enforced via `tenant_id`.

## 18. Billing Model
*   **Table Name:** `invoices`
*   **Purpose:** Local replica of Stripe invoices for dashboard rendering.
*   **Columns:**
    *   `id` | `UUID` | Not Null | Default: `gen_random_uuid()`
    *   `tenant_id` | `UUID` | Not Null | Default: None
    *   `amount_due` | `NUMERIC(10,2)` | Not Null | Default: `0.00`
    *   `status` | `VARCHAR(50)` | Not Null | Default: `'OPEN'`
    *   `pdf_url` | `VARCHAR(1024)` | Nullable | Default: `NULL`
    *   `created_at`, `updated_at` | `TIMESTAMPTZ`
*   **Primary Key:** `(tenant_id, id)`
*   **Foreign Keys:** `FOREIGN KEY (tenant_id) REFERENCES tenants(id)`
*   **Unique Constraints:** None
*   **Check Constraints:** `CHECK (amount_due >= 0)`
*   **Indexes:** None
*   **Relations:** `M:1` with `tenants`
*   **Example Row:** `amount_due: 79.00, status: 'PAID'`
*   **Expected Volume:** 2,000,000 rows
*   **Read/Write Frequency:** Low write / Medium read
*   **Archival Strategy:** Retained for 10 years for tax compliance.
*   **Security Considerations:** Highly sensitive financial records.
*   **Tenant Isolation Rule:** RLS enforced via `tenant_id`.

## 19. Analytics Model
*   **Table Name:** `telemetry_events` *(ClickHouse)*
*   **Purpose:** Ingests millions of raw telemetry rows.
*   **Columns:**
    *   `event_time` | `DateTime` | Not Null | Default: `now()`
    *   `tenant_id` | `UUID` | Not Null | Default: None
    *   `event_type` | `String` | Not Null | Default: None
    *   `entity_id` | `UUID` | Nullable | Default: `NULL`
    *   `device_type` | `String` | Nullable | Default: `NULL`
    *   `session_id` | `String` | Nullable | Default: `NULL`
*   **Primary Key:** `(tenant_id, event_time)`
*   **Foreign Keys:** None
*   **Unique Constraints:** None
*   **Check Constraints:** None
*   **Indexes:** None
*   **Relations:** N/A
*   **Example Row:** `event_type: 'MENU_VIEW', device_type: 'iOS'`
*   **Expected Volume:** 5 Billion+ rows
*   **Read/Write Frequency:** Extreme write / High analytical read
*   **Archival Strategy:** Dropped after 24 months (aggregations kept).
*   **Security Considerations:** Anonymize IP and exact location.
*   **Tenant Isolation Rule:** Filtered strictly by `tenant_id` on query.

## 20. Audit Log Model
*   **Table Name:** `audit_logs`
*   **Purpose:** WORM compliance for critical actions.
*   **Columns:**
    *   `id` | `UUID` | Not Null | Default: `gen_random_uuid()`
    *   `tenant_id` | `UUID` | Not Null | Default: None
    *   `user_id` | `UUID` | Not Null | Default: None
    *   `action` | `VARCHAR(100)` | Not Null | Default: None
    *   `entity_type` | `VARCHAR(100)` | Not Null | Default: None
    *   `entity_id` | `UUID` | Not Null | Default: None
    *   `old_values` | `JSONB` | Nullable | Default: `NULL`
    *   `new_values` | `JSONB` | Nullable | Default: `NULL`
    *   `ip_address` | `INET` | Nullable | Default: `NULL`
    *   `created_at` | `TIMESTAMPTZ` | Not Null | Default: `CURRENT_TIMESTAMP`
*   **Primary Key:** `(tenant_id, created_at, id)`
*   **Foreign Keys:** None (Performance decision to avoid locks)
*   **Unique Constraints:** None
*   **Check Constraints:** None
*   **Indexes:** None
*   **Relations:** N/A
*   **Example Row:** `action: 'UPDATE_PRICE', old_values: {"price": 10}, new_values: {"price": 15}`
*   **Expected Volume:** 50,000,000 rows
*   **Read/Write Frequency:** High write / Low read
*   **Archival Strategy:** Offloaded to S3 Parquet files after 12 months.
*   **Security Considerations:** Immutable, no `UPDATE` or `DELETE` allowed.
*   **Tenant Isolation Rule:** RLS enforced via `tenant_id`.

## 24. Future Ordering Model
*   **Table Name:** `orders`
*   **Purpose:** The central transaction record.
*   **Columns:**
    *   `id` | `UUID` | Not Null | Default: `gen_random_uuid()`
    *   `tenant_id` | `UUID` | Not Null | Default: None
    *   `branch_id` | `UUID` | Not Null | Default: None
    *   `diner_id` | `UUID` | Nullable | Default: `NULL`
    *   `order_number` | `VARCHAR(50)` | Not Null | Default: None
    *   `status` | `VARCHAR(50)` | Not Null | Default: `'PLACED'`
    *   `subtotal` | `NUMERIC(10,2)` | Not Null | Default: `0.00`
    *   `tax_total` | `NUMERIC(10,2)` | Not Null | Default: `0.00`
    *   `grand_total` | `NUMERIC(10,2)` | Not Null | Default: `0.00`
    *   `created_at`, `updated_at`, `deleted_at` | `TIMESTAMPTZ`
*   **Primary Key:** `(tenant_id, id)`
*   **Foreign Keys:** `FOREIGN KEY (tenant_id, branch_id) REFERENCES branches(tenant_id, id)`
*   **Unique Constraints:** `UNIQUE(tenant_id, order_number)`
*   **Check Constraints:** `CHECK (grand_total >= 0)`
*   **Indexes:** `idx_orders_branch_id`
*   **Relations:** `M:1` with `branches`, `1:M` with `order_items`
*   **Example Row:** `order_number: '1042', status: 'PREPARING', grand_total: 45.50`
*   **Expected Volume:** 100,000,000 rows
*   **Read/Write Frequency:** High write / High read
*   **Archival Strategy:** Retained for 7 years.
*   **Security Considerations:** Financial record.
*   **Tenant Isolation Rule:** RLS enforced via `tenant_id`.

## 25. Future Payment Model
*   **Table Name:** `payments`
*   **Purpose:** Tracks payment intents and captures.
*   **Columns:**
    *   `id` | `UUID` | Not Null | Default: `gen_random_uuid()`
    *   `tenant_id` | `UUID` | Not Null | Default: None
    *   `order_id` | `UUID` | Not Null | Default: None
    *   `amount` | `NUMERIC(10,2)` | Not Null | Default: `0.00`
    *   `currency` | `VARCHAR(3)` | Not Null | Default: `'SAR'`
    *   `gateway` | `VARCHAR(50)` | Not Null | Default: `'STRIPE'`
    *   `transaction_ref` | `VARCHAR(255)` | Nullable | Default: `NULL`
    *   `status` | `VARCHAR(50)` | Not Null | Default: `'PENDING'`
    *   `created_at`, `updated_at` | `TIMESTAMPTZ`
*   **Primary Key:** `(tenant_id, id)`
*   **Foreign Keys:** `FOREIGN KEY (tenant_id, order_id) REFERENCES orders(tenant_id, id)`
*   **Unique Constraints:** `UNIQUE(transaction_ref)`
*   **Check Constraints:** None
*   **Indexes:** None
*   **Relations:** `M:1` with `orders`
*   **Example Row:** `gateway: 'APPLE_PAY', amount: 45.50, status: 'CAPTURED'`
*   **Expected Volume:** 100,000,000 rows
*   **Read/Write Frequency:** High write / Medium read
*   **Archival Strategy:** Retained for 7 years.
*   **Security Considerations:** Strictly PCI-DSS scope mapping.
*   **Tenant Isolation Rule:** RLS enforced via `tenant_id`.

## 26. Future POS Integration Model
*   **Table Name:** `pos_sync_jobs`
*   **Purpose:** Tracks sync states with Foodics/Deliverect.
*   **Columns:**
    *   `id` | `UUID` | Not Null | Default: `gen_random_uuid()`
    *   `tenant_id` | `UUID` | Not Null | Default: None
    *   `branch_id` | `UUID` | Not Null | Default: None
    *   `provider` | `VARCHAR(50)` | Not Null | Default: `'FOODICS'`
    *   `status` | `VARCHAR(50)` | Not Null | Default: `'PENDING'`
    *   `error_log` | `TEXT` | Nullable | Default: `NULL`
    *   `last_sync_time` | `TIMESTAMPTZ` | Nullable | Default: `NULL`
    *   `created_at`, `updated_at` | `TIMESTAMPTZ`
*   **Primary Key:** `(tenant_id, id)`
*   **Foreign Keys:** `FOREIGN KEY (tenant_id, branch_id) REFERENCES branches(tenant_id, id)`
*   **Unique Constraints:** None
*   **Check Constraints:** None
*   **Indexes:** None
*   **Relations:** `M:1` with `branches`
*   **Example Row:** `provider: 'FOODICS', status: 'FAILED', error_log: 'Rate limit exceeded'`
*   **Expected Volume:** 5,000,000 rows
*   **Read/Write Frequency:** High write / Low read
*   **Archival Strategy:** Dropped after 30 days (Ephemeral data).
*   **Security Considerations:** None.
*   **Tenant Isolation Rule:** RLS enforced via `tenant_id`.

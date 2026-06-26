# DATABASE ENGINEERING: STRATEGY & ARCHITECTURE

## 1. Database Strategy
The SmartMenu platform leverages a **Polyglot Persistence** strategy.
*   **Primary Relational Database:** PostgreSQL 16+. Used for all core transactional data (Tenants, Menus, Users, Orders). Chosen for its robust ACID compliance, Row Level Security (RLS), JSONB support for sparse data, and mature ecosystem.
*   **Analytics Database:** ClickHouse. PostgreSQL cannot handle 10 million daily telemetry events efficiently without degrading transactional performance. ClickHouse is a columnar OLAP database built for massive aggregations.
*   **Caching & Fast State:** Redis Cluster. Used for session management, rate limiting, and caching the heavily-read menu payloads.
*   **Search:** Typesense or Elasticsearch (future). While Postgres Full-Text Search is acceptable initially, dedicated search engines are required for fuzzy-matching Arabic/English queries across 100,000 tenants.

## 2. Multi-Tenant Data Isolation Strategy
We utilize the **"Pool Model" (Shared Database, Shared Schema)** to manage 100,000 tenants.
*   *Why not Database-per-tenant?* 100,000 databases is operationally impossible to manage and migrate.
*   *Why not Schema-per-tenant?* 100,000 schemas in Postgres causes severe catalog bloat and slows down connection times.
*   **The Pool Implementation:** Every single table belonging to a tenant *must* have a `tenant_id` column.

## 51. Row Level Security Strategy & 52. Tenant Leakage Prevention
To prevent a developer from writing a query that accidentally leaks data across tenants (`SELECT * FROM products` without a `WHERE` clause), we enforce strict **PostgreSQL Row Level Security (RLS)**.
*   All queries must set a local transaction variable: `SET LOCAL myapp.current_tenant_id = 'uuid';`
*   An RLS policy on the table forces: `CREATE POLICY tenant_isolation_policy ON products USING (tenant_id = current_setting('myapp.current_tenant_id')::uuid);`
*   This pushes tenant isolation to the database kernel, acting as an absolute safeguard.

## 39. Partitioning Strategy
Tables expected to grow infinitely (e.g., `audit_logs`, `orders` in the future) will use **PostgreSQL Declarative Partitioning**.
*   **Time-based Partitioning:** `audit_logs` partitioned by month (`audit_logs_2024_01`).
*   **List-based Partitioning:** Future order tables may be partitioned by region (GCC vs Global).

## 40. Sharding Readiness
As we approach the 100,000 tenant mark, a single Postgres primary will reach IOPS limits. We design for sharding from Day 1:
*   `tenant_id` is included in all Primary Keys of tenant-owned data as a composite key (e.g., `PRIMARY KEY (tenant_id, product_id)`). This is the foundation for Citus or CockroachDB to shard data seamlessly in the future.

## 41. Read Replica Strategy
The database handles a 95% Read / 5% Write ratio.
*   1 Primary Node (Read/Write).
*   2-3 Read Replica Nodes.
*   PgBouncer acts as a connection pooler, routing `SELECT` queries to replicas and `INSERT/UPDATE/DELETE` to the primary.

## 42. Caching Data Strategy
*   **Cache Aside:** Applications check Redis first. If miss, query Postgres, then update Redis.
*   **Write-Through:** Critical updates (like marking an item Out of Stock) update Postgres and invalidate Redis simultaneously to prevent serving stale data.

## 43. Soft Delete Strategy
Records are rarely physically deleted.
*   Tables have a `deleted_at` (TIMESTAMP) column.
*   Queries filter by `deleted_at IS NULL`.
*   *Justification:* Required for auditing and restoring data accidentally deleted by a restaurant manager.

## 44. Data Retention Strategy & 54. GDPR/PDPL Readiness
*   **PII Data:** Diner profiles encrypt PII (emails/phones) at rest using `pgcrypto`.
*   **Right to be Forgotten:** A cron job physically deletes soft-deleted PII data after 30 days to comply with GDPR/PDPL.
*   **Archival:** Old audit logs (> 1 year) are exported to S3 (Parquet format) and dropped from Postgres to save expensive block storage.

## 45-47. Backup, Restore, & Migration Strategies
*   **Backup:** Continuous Archiving (WAL shipping) to S3 using tools like pgBackRest. Allows Point-In-Time Recovery (PITR) down to the minute.
*   **Restore:** Automated terraform scripts test restoration to a secondary cluster weekly.
*   **Migration:** Managed by Flyway or Prisma Migrate. Migrations must be backward-compatible (e.g., never `RENAME` a column, instead `ADD` new, copy data, `DROP` old in subsequent deployment).

## 48-49. Seed & Test Data Strategy
*   **Seed Data:** Static data (currencies, standard roles, languages) are inserted during initialization via idempotent scripts.
*   **Test Data:** Synthetic data generators (Faker) create millions of mock rows to test query performance before deploying complex joins.

## 50. Data Validation Rules
Enforced at the DB level via `CHECK` constraints (e.g., `price >= 0`, `email ~* '^[A-Za-z0-9._%-]+@[A-Za-z0-9.-]+[.][A-Za-z]+$'`).

## 53. Auditability
Every insert/update/delete on core tables triggers a PostgreSQL function that writes the old/new row states to the `audit_logs` table, including the `user_id` performing the action.

## 55-58. Risks & Anti-Patterns to Avoid
*   **Risk (Performance):** Missing indexes on foreign keys causing sequential scans during joins.
*   **Risk (Scaling):** Connection exhaustion. *Solution:* PgBouncer.
*   **Risk (Data Consistency):** Relying solely on application-level validations. *Solution:* Use DB-level foreign keys and checks.
*   **Anti-Pattern (EAV):** Entity-Attribute-Value models. We strictly avoid them. Use `JSONB` for sparse data only when the schema is truly unknown.
*   **Anti-Pattern (UUID v4 as Primary Keys in large tables):** UUIDv4 causes index fragmentation. We use **UUIDv7** (time-ordered) or **ULIDs** to ensure sequential B-Tree inserts and minimal fragmentation.

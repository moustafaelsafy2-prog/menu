# ENGINEERING BIBLE: BACKEND & DATABASE

## 1. Service Architecture (The Core API)
The core backend API acts as the transactional engine.
*   **Pattern:** Hexagonal Architecture (Ports and Adapters). The core business logic must be entirely decoupled from the HTTP transport layer and the Database layer.
*   **Layers:**
    1.  **Transport/HTTP:** Controllers, Route Handlers, Input Validation (Zod).
    2.  **Domain/Service:** Core business rules (e.g., `ProductService`). Knows nothing about HTTP.
    3.  **Data/Repository:** Interfaces with Prisma/Database.
*   **Dependency Injection (DI):** Services receive repositories via interfaces. This allows mocking the DB completely during unit tests.

## 2. Validation & Error Handling
*   **Input Validation:** Strict parsing using Zod at the edge. Unknown properties are stripped. Failed validation throws an automatic `422 Unprocessable Entity` formatted to RFC 7807 standard.
*   **Business Validation:** Enforced in the Service layer (e.g., "Cannot delete a category that contains products"). Throws custom `DomainError`.
*   **Error Middleware:** A global error handler catches all uncaught exceptions, logs the stack trace with a `trace_id`, and returns a generic `500 Server Error` to the client.

## 3. Database Transactions
*   **Rule:** Any operation modifying multiple tables MUST be wrapped in a database transaction.
*   *Example:* Creating an Order and deducting Inventory. If step 2 fails, step 1 must rollback.

## 4. Asynchronous Workers & Queues
*   HTTP requests must resolve in < 500ms. If a task takes longer (e.g., Image Processing, Bulk CSV Upload, PDF Generation), it must be offloaded to a Queue.
*   **Technology:** Redis BullMQ or AWS SQS.
*   **Idempotency:** Worker jobs must be idempotent. If a job fails and retries, it must not create duplicate data.

## 5. Event Publishing
*   When a state changes, the service updates the DB and publishes a Domain Event to Kafka (e.g., `ProductPriceUpdated`).
*   **Outbox Pattern:** To guarantee delivery, the event is first saved to an `outbox` table in the same DB transaction. A separate relay process reads the outbox and publishes to Kafka.

## 6. Database Migration Strategy
*   **Tool:** Prisma Migrate (or Flyway/Atlas for raw SQL).
*   **Backward Compatibility:** Migrations MUST be non-breaking. You cannot `DROP COLUMN` or `RENAME COLUMN` if the currently running code depends on it.
*   **The 3-Step Rollout for Breaking Changes:**
    1.  Add new column. Deploy code that writes to both old and new.
    2.  Deploy code that reads from new.
    3.  Drop old column in subsequent migration.

## 7. Database Seeding & Test Data
*   **Seed Strategy:** A `seed.ts` script must exist to populate the DB with necessary constants (Roles, Currencies) on fresh environments.
*   **Test Data:** End-to-End tests use factories to generate synthetic, randomized tenant data. Tests must run in isolated DB schemas to prevent cross-contamination.

## 8. Backup & Rollback Strategy
*   **Backups:** WAL (Write-Ahead Logging) archiving enables Point-in-Time Recovery (PITR).
*   **Rollbacks:** Migrations are considered forward-only. If a bad migration deploys, the fix is to write a *new* migration that reverts the change, not attempting to run `migrate down` in production.

# MICROSERVICES DESIGN (Part 1/2)

## 1. Authentication Service
*   **Responsibilities:** Identity verification, token generation, SSO integration.
*   **Dependencies:** Redis (Session Store).
*   **Public APIs:** `/auth/login`, `/auth/refresh`, `/auth/logout`, `/auth/sso`.
*   **Internal APIs:** `/internal/auth/validate`.
*   **Events Produced:** `UserLoggedIn`, `UserRegistered`, `PasswordResetRequested`.
*   **Queues:** None (Highly synchronous).
*   **Cache Strategy:** Refresh tokens hashed in Redis.
*   **Scaling Strategy:** Horizontally scalable; CPU-bound (hashing).
*   **Database Strategy:** Dedicated PostgreSQL DB for user credentials.
*   **Security:** Argon2 hashing, strict rate limiting on login endpoints.
*   **Failure Scenarios:** If DB goes down, new logins fail, but existing JWTs remain valid until expiry.

## 2. Restaurant Service (Tenant Service)
*   **Responsibilities:** Core tenant data, physical branch locations, operating hours.
*   **Dependencies:** None.
*   **Public APIs:** `/restaurants/{id}` (Read-only).
*   **Internal APIs:** `/internal/tenants/{id}/status`.
*   **Events Produced:** `RestaurantCreated`, `RestaurantUpdated`, `BranchAdded`.
*   **Queues:** Consumes from internal sync queues.
*   **Cache Strategy:** Redis caching for public read endpoints; Edge CDN for static data.
*   **Scaling Strategy:** Read-heavy, heavily cached.
*   **Database Strategy:** CockroachDB (Tenant Table, isolated by TenantId).
*   **Security:** Internal APIs require mTLS.
*   **Failure Scenarios:** If service fails, Edge CDN serves stale restaurant info.

## 3. Menu Service
*   **Responsibilities:** High-level menu aggregation, scheduling (breakfast vs dinner menu).
*   **Dependencies:** Category Service, Product Service.
*   **Public APIs:** `/menus/{id}`, `/menus/active`.
*   **Internal APIs:** None.
*   **Events Produced:** `MenuPublished`, `MenuUnpublished`.
*   **Queues:** Consumes `ItemAvailabilityChanged` to update aggregate state.
*   **Cache Strategy:** The most heavily cached service. Redis + CDN Purge on publish.
*   **Scaling Strategy:** Extreme read horizontal scaling.
*   **Database Strategy:** Read replicas for heavy queries.
*   **Security:** Tenant isolation enforced at the query level.
*   **Failure Scenarios:** Failover to stale Edge cache immediately.

## 4. Category Service
*   **Responsibilities:** Menu structural taxonomy.
*   **Dependencies:** Menu Service.
*   **Public APIs:** `/categories/{id}`.
*   **Internal APIs:** None.
*   **Events Produced:** `CategoryCreated`, `CategoryDeleted`, `CategoryReordered`.
*   **Queues:** None.
*   **Cache Strategy:** Bundled into Menu Service cache payloads.
*   **Scaling Strategy:** Standard horizontal.
*   **Database Strategy:** CockroachDB.
*   **Security:** RBAC restricted to Manager role.
*   **Failure Scenarios:** Standard failover.

## 5. Product Service (Item Service)
*   **Responsibilities:** The core entity (dishes, prices, modifiers, dietary tags).
*   **Dependencies:** Category Service.
*   **Public APIs:** `/products/{id}`, `/products/search`.
*   **Internal APIs:** `/internal/products/validate-price` (used by Commerce).
*   **Events Produced:** `ProductCreated`, `ProductPriceChanged`, `ProductStockChanged`.
*   **Queues:** High volume producer to Kafka.
*   **Cache Strategy:** Redis Hash maps per item.
*   **Scaling Strategy:** Primary write bottleneck; requires sharding by Tenant ID.
*   **Database Strategy:** CockroachDB with aggressive indexing on TenantId + CategoryId.
*   **Security:** Strict validation on price mutations.
*   **Failure Scenarios:** If DB locks, fallback to Redis for reads; writes fail gracefully with 503.

## 6. Media Service
*   **Responsibilities:** Image upload coordination, processing pipeline, metadata extraction.
*   **Dependencies:** Object Storage (S3).
*   **Public APIs:** `/media/upload-url` (presigned S3).
*   **Internal APIs:** `/internal/media/process`.
*   **Events Produced:** `MediaUploadStarted`, `MediaProcessed`, `MediaDeleted`.
*   **Queues:** Consumes massive image processing jobs via RabbitMQ/Kafka.
*   **Cache Strategy:** Edge CDN handles all image serving.
*   **Scaling Strategy:** Auto-scaling worker nodes based on queue depth (GPU/CPU intensive).
*   **Database Strategy:** PostgreSQL for media metadata (URLs, BlurHashes).
*   **Security:** ClamAV virus scanning on upload stream; strict MIME type validation.
*   **Failure Scenarios:** If workers crash, queue builds up; images serve slowly but don't drop.

## 7. Analytics Service
*   **Responsibilities:** Ingesting telemetry, generating BI reports, billing metrics.
*   **Dependencies:** Kafka.
*   **Public APIs:** `/analytics/reports/...`
*   **Internal APIs:** `/internal/analytics/usage` (for Billing Service).
*   **Events Produced:** `ReportGenerated`, `AnomalyDetected`.
*   **Queues:** Consumes millions of telemetry events per minute from Kafka.
*   **Cache Strategy:** Redis caching for complex BI query results (TTL 1 hour).
*   **Scaling Strategy:** Heavy compute requirements for report generation.
*   **Database Strategy:** ClickHouse (Columnar OLAP database).
*   **Security:** Anonymization of diner data before ingestion.
*   **Failure Scenarios:** If Ingestion fails, API Gateway buffers metrics locally temporarily.

## 8. Notification Service
*   **Responsibilities:** Dispatching SMS, Email, and Push notifications.
*   **Dependencies:** SendGrid, Twilio, APNs, FCM.
*   **Public APIs:** None.
*   **Internal APIs:** `/internal/notifications/send`.
*   **Events Produced:** `NotificationSent`, `NotificationFailed`.
*   **Queues:** Consumes from `NotificationQueue` (with retry logic).
*   **Cache Strategy:** None.
*   **Scaling Strategy:** Scales with outbound traffic limits of third-party providers.
*   **Database Strategy:** Simple PostgreSQL for logging delivery statuses.
*   **Security:** API keys for 3rd parties stored in HashiCorp Vault.
*   **Failure Scenarios:** Uses exponential backoff and dead-letter queues (DLQ) for provider outages.

## 9. Billing Service
*   **Responsibilities:** Managing SaaS subscriptions, usage limits, invoicing.
*   **Dependencies:** Stripe API, Analytics Service.
*   **Public APIs:** `/billing/checkout`, `/billing/portal`.
*   **Internal APIs:** `/internal/billing/check-quota`.
*   **Events Produced:** `InvoicePaid`, `SubscriptionCanceled`, `QuotaExceeded`.
*   **Queues:** Consumes Stripe webhooks securely.
*   **Cache Strategy:** Redis cache for fast quota checks (e.g., "Can this tenant add more items?").
*   **Scaling Strategy:** Low volume, high criticality.
*   **Database Strategy:** PostgreSQL (strictly ACID compliant).
*   **Security:** PCI-DSS compliance scope (though actual CC data remains in Stripe).
*   **Failure Scenarios:** If Stripe goes down, queue webhooks; degrade gracefully without locking out tenants instantly.

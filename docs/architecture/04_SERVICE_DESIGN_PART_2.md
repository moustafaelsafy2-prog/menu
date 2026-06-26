# MICROSERVICES DESIGN (Part 2/2)

## 10. Subscription Service (Diner Facing)
*   **Responsibilities:** Managing diner loyalty, marketing subscriptions, and diner profiles.
*   **Dependencies:** Authentication Service.
*   **Public APIs:** `/diner/profile`, `/diner/loyalty`.
*   **Internal APIs:** `/internal/diner/points`.
*   **Events Produced:** `DinerPointsEarned`, `DinerPointsRedeemed`.
*   **Queues:** Consumes `OrderCompleted` events.
*   **Cache Strategy:** Redis for fast profile retrieval.
*   **Scaling Strategy:** Scales with DAU load.
*   **Database Strategy:** PostgreSQL.
*   **Security:** PII data encryption at rest.
*   **Failure Scenarios:** Loyalty points delay if queue backs up; ordering must not block.

## 11. QR Service
*   **Responsibilities:** Generating dynamic URLs, SVG/PNG rendering, tracking scans.
*   **Dependencies:** Analytics Service.
*   **Public APIs:** `/qr/resolve/{hash}` (The actual QR target).
*   **Internal APIs:** `/internal/qr/generate`.
*   **Events Produced:** `QrGenerated`, `QrScanned`.
*   **Queues:** Async generation for bulk QR printing.
*   **Cache Strategy:** Edge computing handles `resolve` routing rules entirely.
*   **Scaling Strategy:** `resolve` endpoint is highly available, deployed to Edge functions.
*   **Database Strategy:** PostgreSQL (Hash-to-URL mappings).
*   **Security:** Rate limiting on generation to prevent resource exhaustion.
*   **Failure Scenarios:** If DB goes down, Edge cache maintains routing rules for active QRs.

## 12. Search Service
*   **Responsibilities:** Fuzzy searching across menus and items.
*   **Dependencies:** Product Service.
*   **Public APIs:** `/search?q={query}`.
*   **Internal APIs:** None.
*   **Events Produced:** None.
*   **Queues:** Consumes `ProductCreated`/`Updated` to reindex.
*   **Cache Strategy:** Query caching in Redis for popular terms.
*   **Scaling Strategy:** High memory compute.
*   **Database Strategy:** Elasticsearch or Meilisearch cluster.
*   **Security:** Tenant isolation enforced via strict indexing filters.
*   **Failure Scenarios:** Search degrades to simple DB `ILIKE` query if Elasticsearch fails.

## 13. Theme Service
*   **Responsibilities:** Compiling dynamic CSS, managing design tokens (fonts, colors).
*   **Dependencies:** None.
*   **Public APIs:** `/theme/config.json`.
*   **Internal APIs:** None.
*   **Events Produced:** `ThemeUpdated`.
*   **Queues:** None.
*   **Cache Strategy:** Output heavily cached via Edge CDN (CSS/JSON rarely changes).
*   **Scaling Strategy:** Static file generation.
*   **Database Strategy:** PostgreSQL for config storage.
*   **Security:** Sanitization of user input to prevent CSS injection attacks.
*   **Failure Scenarios:** Fallback to default platform theme.

## 14. Localization Service
*   **Responsibilities:** Managing translations, LLM automated translations, RTL logic.
*   **Dependencies:** OpenAI/DeepL APIs.
*   **Public APIs:** `/locales/{lang}.json`.
*   **Internal APIs:** `/internal/localize/translate`.
*   **Events Produced:** `TranslationJobCompleted`.
*   **Queues:** Async queues for bulk translating a 500-item menu via LLM.
*   **Cache Strategy:** Immutable CDN caching for published locale files.
*   **Scaling Strategy:** Worker-based scaling for external API calls.
*   **Database Strategy:** PostgreSQL.
*   **Security:** API Key management for LLM providers.
*   **Failure Scenarios:** Degrades to default language (English/Arabic) if translation fails.

## 15. Permission Service
*   **Responsibilities:** Centralized Authorization policy decision point (PDP).
*   **Dependencies:** Authentication Service.
*   **Public APIs:** None.
*   **Internal APIs:** `/internal/permissions/check`.
*   **Events Produced:** `RoleUpdated`, `PermissionsChanged`.
*   **Queues:** None.
*   **Cache Strategy:** Extreme low-latency local memory caching (e.g., OPA in sidecar).
*   **Scaling Strategy:** Deployed as a DaemonSet or Sidecar to eliminate network latency.
*   **Database Strategy:** PostgreSQL for policy storage.
*   **Security:** The absolute core of system security.
*   **Failure Scenarios:** If PDP fails, system fails closed (deny all).

## 16. Settings Service
*   **Responsibilities:** Global tenant configurations (taxes, service charges, tipping rules).
*   **Dependencies:** None.
*   **Public APIs:** `/settings/public`.
*   **Internal APIs:** `/internal/settings/all`.
*   **Events Produced:** `SettingsUpdated`.
*   **Queues:** None.
*   **Cache Strategy:** Bundled into Edge menu payload.
*   **Scaling Strategy:** Standard read-heavy.
*   **Database Strategy:** PostgreSQL (JSONB column for flexible config).
*   **Security:** Strict validation of tax/financial inputs.
*   **Failure Scenarios:** Failover to Redis cache.

## 17. Activity Service
*   **Responsibilities:** Aggregating notifications, internal messages, and staff alerts.
*   **Dependencies:** Notification Service.
*   **Public APIs:** `/activity/feed`.
*   **Internal APIs:** `/internal/activity/log`.
*   **Events Produced:** None.
*   **Queues:** Consumes from internal Kafka topics (e.g., `OrderReceived`).
*   **Cache Strategy:** Redis pub/sub for real-time dashboard alerts.
*   **Scaling Strategy:** WebSocket connection scaling.
*   **Database Strategy:** TimescaleDB or fast PostgreSQL for chronological feeds.
*   **Security:** Tenant/User isolation.
*   **Failure Scenarios:** Feed delays; non-critical to core business.

## 18. Audit Service
*   **Responsibilities:** Immutable compliance logging (Who did what, when).
*   **Dependencies:** All services.
*   **Public APIs:** None (Admin only).
*   **Internal APIs:** `/internal/audit/record`.
*   **Events Produced:** None.
*   **Queues:** Consumes `AuditLogGenerated` events from Kafka.
*   **Cache Strategy:** None (Write-heavy).
*   **Scaling Strategy:** High throughput write scaling.
*   **Database Strategy:** Append-only Datastore (e.g., AWS QLDB or heavily restricted Postgres).
*   **Security:** WORM (Write Once, Read Many) compliance. Cannot be altered by admins.
*   **Failure Scenarios:** Kafka buffers events if DB goes down; zero data loss acceptable.

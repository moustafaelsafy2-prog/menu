# ARCHITECTURE: DIAGRAMS & REQUEST LIFECYCLES

## 1. System Context Diagram (Conceptual)
**External Entities:**
*   **Diner:** Interacts via Mobile Browser (PWA).
*   **Restaurant Staff:** Interacts via Admin Dashboard & Floor PWA.
*   **POS Systems (Foodics, Deliverect):** Interacts via Inbound/Outbound Webhooks and APIs.
*   **Payment Gateways (Stripe, Apple Pay):** Interacts via secure API.
*   **SaaS Billing (Stripe Billing):** Interacts via secure API.

**System Boundary:** The SmartMenu Headless OS sits in the center, acting as the routing and data layer between the Staff (Write Operations), the Diner (Read Operations), and External Integrations.

## 2. Container Diagram (High-Level Structure)
*   **Edge Layer:** Global CDN, Edge Compute Functions (routing, hydration).
*   **API Gateway:** Routes all B2B and B2C traffic, handles rate limiting, TLS termination, and JWT validation.
*   **Microservices Cluster:** Kubernetes pods hosting the individual domain services (Catalog, Media, Tenant, etc.).
*   **Event Broker:** Apache Kafka cluster handling async communication between services.
*   **Data Layer:**
    *   Distributed SQL (CockroachDB) for core relational data.
    *   Redis Cluster for caching, rate limiting, and real-time state.
    *   ClickHouse for Analytics OLAP queries.
    *   S3-compatible Object Storage for Media.

## 3. Request Lifecycles

### 3.1. Authentication Flow
1.  User accesses login page.
2.  Credentials/SSO submitted to Identity Provider (IdP).
3.  IdP authenticates and returns an authorization code.
4.  Frontend exchanges code for short-lived JWT Access Token and long-lived HttpOnly secure Refresh Token via API Gateway.
5.  API Gateway validates JWT signature on subsequent requests without hitting the DB.

### 3.2. Authorization Flow
1.  API Gateway validates JWT authenticity.
2.  Request is passed to the target Service.
3.  Target Service inspects JWT claims (Tenant ID, Role).
4.  If complex permission is needed, Target Service queries Permission Service.
5.  Access is Granted or Denied (403 Forbidden).

### 3.3. Restaurant Onboarding Flow
1.  User signs up via marketing site.
2.  Tenant Service creates a new Tenant record in the DB (sharding key generated).
3.  Tenant Service fires `TenantCreated` event to Kafka.
4.  Billing Service consumes event, creates Stripe Customer.
5.  Settings Service consumes event, applies default theme templates.
6.  Notification Service consumes event, sends Welcome Email.

### 3.4. Customer Menu Flow (The "Edge-First" Pattern)
1.  Diner scans QR code, resolving to Edge CDN.
2.  CDN attempts to serve cached Static HTML shell.
3.  *Cache Hit:* Returns HTML instantly (10ms).
4.  *Cache Miss:* Edge Compute function fetches JSON payload from Redis (via API Gateway), renders HTML, caches it, and returns it.
5.  PWA hydrates on client device.
6.  PWA establishes SSE (Server-Sent Events) connection to Realtime Service for live availability updates.

### 3.5. Dashboard Flow
1.  Manager opens dashboard (SPA).
2.  SPA fetches data via API Gateway (GraphQL/REST).
3.  API Gateway routes to Catalog Service.
4.  Catalog Service queries Primary Database.
5.  Data returned to Manager.

### 3.6. Subscription Flow
1.  Owner selects "Upgrade to Pro" in Dashboard.
2.  Billing Service generates Stripe Checkout session.
3.  Owner completes payment on Stripe.
4.  Stripe fires Webhook to API Gateway -> Billing Service.
5.  Billing Service updates subscription status in DB and fires `SubscriptionUpgraded` event.
6.  Permission Service updates tenant capabilities.

### 3.7. QR Flow
1.  Manager requests QR generation.
2.  QR Service generates high-res SVG payload containing the unique routing URL.
3.  QR Service uploads SVG to Object Storage.
4.  URL of SVG is returned to Dashboard for download/printing.

### 3.8. Media Upload Flow
1.  Manager uploads 10MB photo of a burger.
2.  Dashboard requests signed S3 upload URL from Media Service.
3.  Dashboard uploads file *directly* to S3 bucket (bypassing our servers).
4.  S3 fires `ObjectCreated` event to Event Broker.
5.  Media Service begins Image Processing Flow.

### 3.9. Image Processing Flow
1.  Media Service consumes `ObjectCreated` event.
2.  Worker downloads raw image.
3.  Worker generates WebP, AVIF, and heavily compressed BlurHash.
4.  Worker resizes to multiple responsive breakpoints.
5.  Worker uploads processed assets to CDN origin bucket.
6.  Worker fires `MediaProcessed` event.
7.  Catalog Service updates item record with new CDN URLs.

### 3.10. Caching Flow
1.  Manager updates price of Burger from $10 to $12.
2.  Catalog Service updates Primary DB.
3.  Catalog Service fires `ItemUpdated` event.
4.  Cache Invalidation Worker consumes event.
5.  Worker issues PURGE request to Edge CDN for that specific Tenant's menu.
6.  Worker updates Redis cache.

### 3.11. Realtime Flow
1.  Manager marks "Truffle Pasta" as Sold Out.
2.  Catalog Service updates DB and fires `ItemAvailabilityChanged` event.
3.  Realtime Service (WebSocket/SSE server) consumes event.
4.  Realtime Service looks up all active connections for that Tenant ID.
5.  Pushes lightweight JSON payload: `{itemId: 'xyz', status: '86'}` to connected diners.
6.  Diner's UI strikes through the item instantly.

### 3.12. Analytics Flow
1.  Diner views an item for > 3 seconds.
2.  PWA fires telemetry batch to API Gateway.
3.  Gateway routes to Analytics Ingestion Service.
4.  Ingestion Service pushes raw data directly into Kafka topic `MenuViews`.
5.  ClickHouse consumer reads from Kafka and inserts data in massive batches.
6.  Dashboard queries ClickHouse for real-time aggregations.

### 3.13. Notification Flow
1.  Order is placed (or system event occurs).
2.  System fires `NotificationRequested` event (e.g., Target: Manager, Channel: SMS).
3.  Notification Service consumes event.
4.  Service checks Manager's preference (e.g., Do Not Disturb hours).
5.  Service formats payload and dispatches to Twilio.

### 3.14. Audit Flow
1.  Manager deletes a Category.
2.  Catalog Service performs deletion.
3.  Catalog Service fires `ResourceDeleted` event with user context (JWT claims).
4.  Audit Service consumes event.
5.  Audit Service writes immutable log entry to cold storage (e.g., append-only DB table).

### 3.15. Logging Flow
1.  Any service emits structured JSON log to stdout.
2.  DaemonSet (e.g., FluentBit) captures stdout on the Kubernetes node.
3.  FluentBit enriches log with trace ID and pod metadata.
4.  FluentBit ships log to centralized logging cluster (Elasticsearch/Datadog).

### 3.16. Monitoring Flow
1.  Prometheus scrapes `/metrics` endpoint on every service every 15 seconds.
2.  Metrics are stored in Time Series Database.
3.  Grafana visualizes metrics.
4.  Alertmanager triggers PagerDuty if Error Rate > 1% or P99 Latency > 500ms.

### 3.17. Error Handling Flow
1.  Service encounters unhandled exception.
2.  Exception is caught by global middleware.
3.  Middleware formats standardized RFC 7807 Problem Detail JSON response.
4.  Middleware logs stack trace with Trace ID.
5.  API Gateway returns generic 500 error to client (hiding internal details).

### 3.18. Backup Flow
1.  CronJob triggers nightly script.
2.  CockroachDB executes distributed backup command.
3.  Backup files are streamed directly to encrypted, versioned S3 bucket in a separate physical region.

### 3.19. Restore Flow
1.  DevOps initiates restore via CLI.
2.  CockroachDB points to S3 bucket.
3.  Database streams data back, achieving point-in-time recovery.

### 3.20. Disaster Recovery Flow
1.  Primary region (e.g., AWS me-south-1) goes entirely offline.
2.  Global DNS routing (Route53/Cloudflare) automatically fails over to Secondary region (eu-central-1).
3.  Secondary region spins up infrastructure via Terraform.
4.  Secondary region database promotes itself from read-replica to primary.
5.  Platform resumes operations (RTO < 15 minutes, RPO < 5 minutes).

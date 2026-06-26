# EVENT-DRIVEN ARCHITECTURE (EDA)

## 1. EDA Philosophy
The platform relies on Apache Kafka as the central nervous system. Services do not call each other synchronously for state changes; instead, they emit Domain Events. This ensures decoupling, allows new services to be added without modifying existing ones, and provides a replayable log of all system activity.

## 2. Topic/Queue Structure
Topics are partitioned by `TenantId` to ensure total ordering of events for a specific restaurant while allowing massive horizontal scalability across thousands of restaurants.

*   `catalog.events`
*   `tenant.events`
*   `media.events`
*   `commerce.events`
*   `billing.events`
*   `telemetry.events`
*   `audit.events`

## 3. Event Catalog

### 3.1. Tenant Events (`tenant.events`)
*   **`TenantCreated`**
    *   **Producer:** Tenant Service
    *   **Consumers:** Billing Service (creates Stripe account), Settings Service (creates defaults), Notification Service (Welcome Email).
*   **`BranchAdded`**
    *   **Producer:** Tenant Service
    *   **Consumers:** Catalog Service (initializes branch menu), QR Service (generates default table QRs).

### 3.2. Catalog Events (`catalog.events`)
*   **`ItemAvailabilityChanged`** (The highest priority operational event)
    *   **Producer:** Product Service (triggered via Staff Dashboard)
    *   **Consumers:**
        *   Realtime Service (pushes WebSocket update to active diners instantly).
        *   Menu Service (updates aggregate cache).
        *   Edge Cache Worker (purges specific CDN tags).
        *   POS Integration Worker (sends webhook to Foodics/Deliverect to 86 the item externally).
*   **`ItemPriceUpdated`**
    *   **Producer:** Product Service
    *   **Consumers:** Menu Service, Edge Cache Worker, Audit Service.
*   **`MenuPublished`**
    *   **Producer:** Menu Service
    *   **Consumers:** Search Service (reindexes Elasticsearch), Edge Cache Worker.

### 3.3. Media Events (`media.events`)
*   **`RawImageUploaded`**
    *   **Producer:** S3/Object Storage Webhook (via API Gateway)
    *   **Consumers:** Image Processing Worker (generates WebP, AVIF, BlurHash).
*   **`ImageProcessingCompleted`**
    *   **Producer:** Image Processing Worker
    *   **Consumers:** Product Service (updates DB with CDN URLs), Notification Service (alerts staff if they were waiting).

### 3.4. Commerce Events (`commerce.events`)
*   **`OrderPlaced`**
    *   **Producer:** Commerce Service
    *   **Consumers:**
        *   KDS (Kitchen Display) Worker.
        *   POS Integration Worker.
        *   Notification Service (alerts waiter).
        *   Analytics Service.
*   **`PaymentSucceeded`**
    *   **Producer:** Commerce Service (via Stripe Webhook)
    *   **Consumers:** Order Service (changes status to Paid), Subscription Service (adds loyalty points).

### 3.5. Billing Events (`billing.events`)
*   **`SubscriptionUpgraded`**
    *   **Producer:** Billing Service
    *   **Consumers:** Permission Service (unlocks Pro features), Notification Service.
*   **`PaymentFailed`**
    *   **Producer:** Billing Service
    *   **Consumers:** Tenant Service (initiates grace period logic), Notification Service.

### 3.6. Telemetry Events (`telemetry.events`)
*   **`MenuViewed`**, **`ItemViewed`**, **`ItemAddedToCart`**
    *   **Producer:** Diner PWA (batched via API Gateway)
    *   **Consumers:** ClickHouse Ingestion Worker, Real-time Dashboard Worker.

## 4. Workers & Consumers (The Compute Layer)
*   **Edge Cache Invalidation Worker:** Listens to Catalog events. Issues exact URL or Tag-based PURGE requests to Cloudflare/Vercel to ensure sub-second cache invalidation globally.
*   **POS Sync Worker:** A robust worker with internal retry logic (exponential backoff) and dead-letter queues. POS systems (like Micros or Foodics) are famously unreliable; this worker ensures SmartMenu never loses state if the external POS goes down.
*   **Image Processing Worker:** GPU/CPU heavy pods scaling automatically based on the depth of the `RawImageUploaded` queue.

## 5. Event Schema & Contracts
All events adhere strictly to the **CloudEvents** specification to ensure standardized routing.
Schemas are registered in a central **Schema Registry** (Protobuf/Avro) to prevent a producer from emitting an event that breaks a consumer.

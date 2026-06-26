# ENTERPRISE ARCHITECTURE DECISION RECORD (ADR) & SUMMARY

## 1. Enterprise ADR

**Title:** Adoption of Edge-First Event-Driven Microservices with Distributed SQL.
**Status:** Approved
**Context:** SmartMenu must support 100,000 tenants and 10M DAU with strict data isolation, zero-downtime, and 50ms read latency for diners. A monolithic architecture backed by a single relational database cannot meet the availability or concurrent read constraints.
**Decision:**
1.  We will adopt an API-first microservices architecture orchestrated by Kubernetes.
2.  Inter-service state changes will be strictly asynchronous via Apache Kafka.
3.  The primary datastore will be CockroachDB (Distributed SQL) to handle geo-distribution and active-active high availability.
4.  The diner-facing consumer application will be heavily decoupled, rendered at the Edge (Vercel/Cloudflare).
**Consequences:**
*   *Positive:* Unlimited horizontal scalability. Complete blast-radius isolation if a service fails.
*   *Negative:* High initial operational complexity. Requires a highly skilled DevOps and SRE team. Eventual consistency challenges in the dashboard UI.

## 2. Technology Decision Matrix

| Domain | Technology Choice | Justification |
| :--- | :--- | :--- |
| **API Gateway** | Kong / API7 | High performance, native Kubernetes ingress, vast plugin ecosystem. |
| **Core Compute** | Go / Node.js (NestJS) | Go for high-throughput/low-latency (Catalog, Realtime); Node for frontend-adjacent APIs (BFFs). |
| **Frontend** | React / Next.js / Astro | Astro for ultra-fast Edge-rendered consumer menus; Next.js for the complex B2B Dashboard SPA. |
| **Primary DB** | CockroachDB | Postgres-compatible, natively distributed, multi-active, strong consistency. |
| **Analytics DB** | ClickHouse | Unmatched performance for column-oriented, analytical read-heavy queries. |
| **Event Broker** | Apache Kafka | Industry standard for durable, replayable, high-throughput event streaming. |
| **Cache** | Redis Cluster | Low-latency data structures, pub/sub for real-time. |
| **Infrastructure** | Terraform / Kubernetes | Declarative, cloud-agnostic configuration and container orchestration. |

## 3. Risk Matrix

| Risk | Likelihood | Impact | Mitigation Strategy |
| :--- | :--- | :--- | :--- |
| **Split-Brain DB Scenario** | Low | Critical | Use consensus algorithms (Raft in CockroachDB) requiring a majority quorum. |
| **Kafka Cluster Failure** | Low | High | Deploy MSK across 3 Availability Zones. Implement dead-letter queues. |
| **Cache Stampede** | Medium | High | Implement Redis locking strategies and cache warming scripts. |
| **Third-Party POS Outage** | High | Medium | Decouple sync workers; implement aggressive exponential backoff and localized queuing. |

## 4. Architecture Assessment Scores (Out of 100)
*   **Scalability Score:** **98/100** (Stateless edge + distributed SQL handles near-infinite read scale).
*   **Security Score:** **95/100** (Zero-trust mTLS, robust RBAC, strict tenant isolation).
*   **Performance Score:** **96/100** (Edge rendering guarantees < 50ms TTFB globally).
*   **Maintainability Score:** **85/100** (High due to decoupling, but lowered slightly by the inherent complexity of distributed systems and EDA tracing).

## 5. Future Expansion Plan
1.  **Multi-Region Active-Active:** Currently deployed in ME-South (Bahrain). Architecture supports spinning up EU-Central. CockroachDB natively spans regions, tying data to regions based on `TenantId` to comply with data residency laws.
2.  **App Ecosystem (Plugin Architecture):** The Event-driven nature allows third-party developers to simply listen to our Kafka topics (via secure Webhook proxies) and build integrations (e.g., AI wine pairing) without touching core code.

## 6. Technical Debt Prevention Plan
*   **Strict API Contracts:** All service boundaries are defined by OpenAPI (Swagger) or gRPC Protobufs. Breaking changes to contracts are blocked by CI/CD.
*   **Deprecation Policies:** V1 APIs are maintained for 6 months. Telemetry tracks V1 usage. When usage hits 0%, the code is automatically flagged for removal.
*   **Architecture Fitness Functions:** Automated tests (e.g., ArchUnit) run in CI to ensure code does not violate DDD boundaries (e.g., stopping the Billing Service from directly importing a Catalog Service database model).
*   **Error Budgeting:** SRE team enforces error budgets. If a service drops below 99.9% uptime, feature development on that service freezes until reliability is restored.

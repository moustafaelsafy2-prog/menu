# CROSS-CUTTING CONCERNS

## 1. Caching Strategy
A multi-tiered caching architecture is critical for achieving 50ms render times and shielding the database from 10M DAU.
*   **Tier 1: Browser Cache (Service Workers):** The PWA aggressively caches the HTML shell and static assets (CSS/JS) locally.
*   **Tier 2: Global Edge CDN (Cloudflare/Vercel):** The fully hydrated JSON/HTML representations of menus are cached at Edge nodes in the Middle East (Bahrain, Dubai). *Invalidation:* Tag-based purging via Kafka workers (e.g., purge tag `tenant-1234-menu`).
*   **Tier 3: Redis In-Memory Cache:** Sits in front of the primary DB. Stores aggregated menu payloads, tenant configurations, and RBAC policies.
*   **Tier 4: Database Cache:** CockroachDB's internal memory buffers.
*   **Image Caching:** Handled entirely by specialized image CDNs (e.g., Cloudinary/Imgix pattern) with immutable URLs based on content hashes.

## 2. Security Architecture
*   **Zero Trust Network:** No service trusts another. All inter-service communication over gRPC/HTTP requires mTLS (Mutual TLS) managed by a service mesh (Istio/Linkerd).
*   **Identity & Access:**
    *   Auth0 handles identity. Frontend receives short-lived JWTs (15 mins) and HttpOnly secure Refresh Tokens.
    *   RBAC is enforced at the API Gateway *and* the Service layer via the Permission Service (OPA).
*   **Tenant Isolation:** Row-Level Security (RLS) is strictly enforced in CockroachDB. Every query must include a `TenantId` derived from the validated JWT.
*   **API Security & DDoS:** Cloudflare WAF protects against volumetric attacks. The API Gateway enforces strict rate limiting per IP and per Tenant.
*   **Data Security:** AES-256 encryption at rest. TLS 1.3 in transit.
*   **File Security:** Direct-to-S3 uploads use pre-signed URLs. Uploads land in a "quarantine" bucket, are scanned by ClamAV, processed, and moved to the public bucket.

## 3. Scalability Strategy
*   **Horizontal Compute:** Kubernetes HPA (Horizontal Pod Autoscaler) scales stateless microservices based on CPU/Memory and custom metrics (e.g., Kafka lag).
*   **Vertical Scaling:** Reserved for heavy data processing nodes (ClickHouse aggregators).
*   **Database Scaling:** CockroachDB (Distributed SQL) natively scales out by adding nodes, automatically rebalancing data ranges while maintaining ACID compliance.
*   **Read Replicas:** Handled intrinsically by CockroachDB's follower reads capability.
*   **Connection Pooling:** PgBouncer sits between microservices and the database to prevent connection exhaustion during connection spikes.

## 4. Observability Strategy
*   **Logs:** All services output structured JSON to `stdout`. FluentBit ships them to Datadog/Elasticsearch.
*   **Metrics:** Prometheus scrapes `/metrics` endpoints. Grafana visualizes (P99 latency, Error Rates, CPU).
*   **Distributed Tracing:** OpenTelemetry (OTel) injects Trace IDs at the API Gateway. The ID propagates across HTTP, gRPC, and Kafka messages, allowing full lifecycle visualization in Jaeger/Datadog.
*   **Alerting:** PagerDuty integration triggers on SLA breaches (e.g., "Menu API 5xx rate > 1% for 3 mins").

## 5. CI/CD Architecture
*   **Development:** Engineers use ephemeral environments (e.g., DevSpace) to test against a cloud-like setup locally.
*   **Testing:** GitHub Actions runs Unit, Integration, and Contract tests (Pact) on every PR. SonarQube runs SAST.
*   **Preview:** Vercel automatically creates preview URLs for frontend branches.
*   **Staging:** Merges to `main` deploy to Staging. Automated E2E testing (Playwright) runs against Staging.
*   **Production:** Automated deployment via ArgoCD (GitOps). Changes to infrastructure manifests trigger rolling updates.
*   **Rollback:** Handled automatically by ArgoCD if post-deployment health checks fail.

## 6. Deployment Infrastructure
*   **Frontend / Edge Compute:** Vercel or Cloudflare Workers.
*   **Core Microservices:** Amazon EKS (Elastic Kubernetes Service) or GCP GKE.
*   **Primary Database:** CockroachDB Dedicated or Self-Hosted on Kubernetes.
*   **Cache & Event Bus:** ElastiCache (Redis) and MSK (Managed Streaming for Apache Kafka).
*   **Storage:** Amazon S3 (Standard for active, Glacier for backups).

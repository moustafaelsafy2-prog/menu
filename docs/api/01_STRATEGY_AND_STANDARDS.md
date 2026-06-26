# API ENGINEERING: STRATEGY & STANDARDS

## 1. API Strategy & 3. Public vs Private vs Internal
SmartMenu utilizes a Hybrid API Strategy routed entirely through a central API Gateway (e.g., Kong).
*   **Public Edge APIs (`/v1/edge/*`):** Hyper-optimized, strictly read-only JSON endpoints served primarily from Edge CDNs. Used exclusively by the consumer Diner PWA. No authentication required, extremely high rate limits, aggressive caching (ETags, `Cache-Control`).
*   **Private B2B APIs (`/v1/admin/*`, `/v1/tenant/*`):** RESTful APIs for the restaurant dashboard and platform super-admins. Strictly authenticated via JWT, tenant-isolated, low rate limits, minimal caching.
*   **Internal Service APIs (`/internal/*`):** gRPC or HTTP endpoints used *only* for microservice-to-microservice communication behind the VPC. Blocked at the external API Gateway. Requires mTLS.
*   **Webhook APIs (`/v1/webhooks/*`):** Dedicated ingest endpoints for Stripe, POS systems, etc. Strict payload signature verification required.

## 2. API Versioning Strategy
*   URI Versioning: `api.smartmenu.com/v1/...`
*   Major versions (v1, v2) are used for sweeping, backward-incompatible architectural changes.
*   Minor additive changes (new fields) do not bump the version.
*   Sunset Policy: Deprecated versions emit a `Deprecation: true` header 6 months prior to removal.

## 4. Authentication Model & 5. Authorization Model
*   **Authn:** Short-lived JWT Access Tokens (15m TTL) passed in `Authorization: Bearer <token>`. Long-lived HttpOnly Secure Refresh Tokens (7d TTL) used to obtain new Access Tokens.
*   **Authz:** Role-Based Access Control (RBAC). The JWT payload contains `tenant_id` and `roles`. The API Gateway validates the JWT signature; the underlying service checks the `roles` against the requested endpoint's required permissions.

## 6. Tenant Resolution & 7. Tenant Isolation Rules
*   **Rule 1:** A B2B request *must* resolve to a specific tenant.
*   **Resolution:** Extracted strictly from the validated JWT payload (`tenant_id` claim). The client *never* passes the tenant ID in the URL or body for B2B ops (prevents Broken Object Level Authorization - BOLA/IDOR).
*   **Edge Resolution:** For public menus, the tenant is resolved via the subdomain/slug (e.g., `albaik.smartmenu.com` or `sm.to/hash`) translated by the Gateway into a backend `tenant_id` query.

## 8. Rate Limiting Strategy
*   **B2B APIs:** 100 requests / minute / IP.
*   **Edge APIs:** 1,000 requests / minute / IP (to handle dinner rushes).
*   **Auth APIs:** 5 requests / minute / IP (Brute-force protection).
*   Implemented via API Gateway using Redis counters. Returns `429 Too Many Requests` with `Retry-After` header.

## 9-12. Pagination, Filtering, Sorting, Search
*   **Pagination:** Cursor-based (`?cursor=xyz123&limit=50`) for high-performance, infinite-scroll lists (Products, Logs). Offset-based (`?page=2&limit=50`) allowed only for low-volume admin tables.
*   **Filtering:** Query parameters using exact matches (`?status=ACTIVE`) or operators (`?price[gte]=10`).
*   **Sorting:** `?sort=-created_at,price` (Minus denotes descending).
*   **Search:** `?q=burger`. Triggers a fuzzy search via Elasticsearch/Typesense, avoiding Postgres `LIKE` bottlenecks.

## 13. Validation Strategy & 14-15. Response Standards
*   Input validation occurs at the routing layer before hitting business logic.
*   **Success Response (2xx):**
    ```json
    {
      "data": { "id": "123", "name": "Burger" },
      "meta": { "cursor": "next_page_token" }
    }
    ```
*   **Error Response (4xx/5xx) - RFC 7807 Standard:**
    ```json
    {
      "type": "https://api.smartmenu.com/errors/validation-failed",
      "title": "Validation Failed",
      "status": 422,
      "detail": "The 'price' field must be greater than 0.",
      "trace_id": "req_abc123"
    }
    ```

## 18. Idempotency Strategy
*   All `POST` endpoints creating financial or critical resources (Orders, Payments, Subscriptions) require an `Idempotency-Key` header.
*   The API Gateway caches the response against this key for 24 hours. Duplicate requests with the same key return the exact cached response without re-executing logic.

## 19-21. Tracing, Correlation, & Logging
*   API Gateway injects an `X-Trace-Id` header into every incoming request.
*   This ID is passed to all downstream microservices, Kafka events, and logs.
*   Logs are JSON formatted. No PII (passwords, emails, tokens) is ever logged.

## 22-26. Security, CORS, CSRF, Files
*   **CORS:** B2B APIs restricted to `dashboard.smartmenu.com`. Edge APIs allow `*` origin for public access.
*   **CSRF:** B2B SPA uses anti-CSRF tokens synced with cookies since refresh tokens are HttpOnly.
*   **File Uploads:** APIs never accept binary data directly. The API issues a short-lived Pre-signed S3 URL. The client uploads directly to S3.
*   **Media Delivery:** Served purely via CDN. Original files are never exposed. Only processed WebP/AVIF variants are accessible.

## 55-60. Gateway, Edge, & Caching Strategies
*   **Gateway:** Kong API Gateway handles Rate Limiting, JWT Validation, CORS, and Request Routing.
*   **Edge:** The `/v1/edge/menu/*` endpoints are physically deployed to Vercel/Cloudflare Workers.
*   **Cache Headers:** Edge APIs return `Cache-Control: public, s-maxage=3600, stale-while-revalidate=86400`.
*   **ETag:** Every Edge response includes an ETag hash of the payload. Clients send `If-None-Match`. If unchanged, API returns `304 Not Modified` (0 bytes payload).
*   **Invalidation:** When a restaurant manager updates a price, a Kafka event triggers a CDN Purge API call targeting the specific `tenant_id` cache tag.

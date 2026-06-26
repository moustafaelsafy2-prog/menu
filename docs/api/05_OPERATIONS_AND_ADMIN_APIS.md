# API ENGINEERING: OPERATIONS, ADMIN & FUTURE APIs

## 42-43. Subscription & Billing API Design

### Create Checkout Session
*   **Endpoint:** Upgrade Plan Checkout
*   **Method / Path:** `POST /v1/tenant/billing/checkout`
*   **Purpose:** Generates a Stripe Checkout session URL for plan upgrades.
*   **Required Role:** `tenant:owner`
*   **Request Body:** `{ "plan_id": "pro_monthly" }`
*   **Response Body:** `{ "data": { "checkout_url": "https://checkout.stripe.com/..." } }`

### Stripe Webhook
*   **Endpoint:** Payment Webhook
*   **Method / Path:** `POST /v1/webhooks/stripe`
*   **Access Level:** Public (Restricted by Signature)
*   **Security:** Validates `Stripe-Signature` header against the configured webhook secret.

## 44. Analytics API Design

### Get Menu Performance
*   **Endpoint:** Menu Analytics
*   **Method / Path:** `GET /v1/tenant/analytics/menu-performance`
*   **Purpose:** Fetches aggregated view counts from ClickHouse.
*   **Required Role:** `analytics:read`
*   **Request Params:** `date_from`, `date_to`, `branch_id`.
*   **Response Body:** `{ "data": { "total_views": 15400, "top_items": [...] } }`

### Ingest Telemetry (Edge API)
*   **Endpoint:** Track Event
*   **Method / Path:** `POST /v1/edge/telemetry`
*   **Access Level:** Public
*   **Request Body:** `{ "tenant_slug": "albaik", "event_type": "ITEM_VIEW", "entity_id": "..." }`
*   **Rate Limit:** 500/min per IP.

## 49-50. Audit & Activity API Design

### List Audit Logs
*   **Endpoint:** Get Audit Trail
*   **Method / Path:** `GET /v1/tenant/audit-logs`
*   **Required Role:** `tenant:admin`
*   **Purpose:** Fetches immutable compliance logs.
*   **Response Body:** `{ "data": [ { "action": "UPDATE_PRICE", "user": "tariq@...", "timestamp": "..." } ] }`

## 29. Admin API Design (Superadmin Platform)
*These endpoints operate at the platform level, circumventing standard `tenant_id` resolution.*

### List All Tenants
*   **Endpoint:** Global Tenant List
*   **Method / Path:** `GET /v1/admin/tenants`
*   **Required Role:** `superadmin` (Checked at API Gateway).

### Suspend Tenant
*   **Endpoint:** Suspend Account
*   **Method / Path:** `POST /v1/admin/tenants/{tenant_id}/suspend`
*   **Request Body:** `{ "reason": "Non-payment" }`
*   **Security:** Immediately revokes all active JWTs for that tenant's staff and purges public Edge cache.

## 51. Webhook API Design (Outbound)

### Register Webhook Endpoint
*   **Endpoint:** Create Webhook
*   **Method / Path:** `POST /v1/tenant/webhooks`
*   **Purpose:** Allows enterprise tenants to listen to internal platform events.
*   **Request Body:** `{ "target_url": "https://api.their-server.com", "events": ["menu.published", "product.sold_out"] }`

---

# FUTURE-PROOFING APIs

## 52. Future Ordering API Design
*   **Endpoint:** Create Order
*   **Method / Path:** `POST /v1/edge/orders`
*   **Purpose:** Allows diners to place a table-side order. Requires `Idempotency-Key` header.
*   **Request Body:** `{ "branch_id": "...", "table_number": "12", "items": [ { "product_id": "...", "qty": 2 } ] }`

## 53. Future Payment API Design
*   **Endpoint:** Initiate Payment
*   **Method / Path:** `POST /v1/edge/orders/{order_id}/pay`
*   **Request Body:** `{ "provider": "APPLE_PAY", "payment_token": "..." }`

## 54. Future POS Integration API Design
*   **Endpoint:** Sync POS Catalog
*   **Method / Path:** `POST /v1/tenant/integrations/pos/sync`
*   **Purpose:** Manually triggers a pull from Foodics/Deliverect to overwrite the SmartMenu catalog.
*   **Status Codes:** 202 Accepted (Triggers background Kafka worker).

# API ENGINEERING: TENANT & CORE APIs

*Note: For all endpoints below under `/v1/tenant/*`, the `tenant_id` is automatically resolved from the caller's JWT. This prevents IDOR/BOLA attacks.*

## 31. Restaurant API Design

### Get Current Tenant
*   **Endpoint:** Get Tenant
*   **Method / Path:** `GET /v1/tenant`
*   **Purpose:** Retrieves the core details of the active workspace.
*   **Access Level:** Any authenticated tenant user.
*   **Response Body:** `{ "data": { "id": "...", "name": "Albaik", "slug": "albaik" } }`

### Update Tenant
*   **Endpoint:** Update Tenant
*   **Method / Path:** `PATCH /v1/tenant`
*   **Purpose:** Updates workspace details (e.g., changing the public slug).
*   **Required Role:** `tenant:admin`
*   **Request Body:** `{ "name": "Albaik Group", "slug": "albaik-group" }`
*   **Status Codes:** 200 OK, 409 Conflict (Slug already taken).

### Create Brand (Restaurant Concept)
*   **Endpoint:** Create Brand
*   **Method / Path:** `POST /v1/tenant/brands`
*   **Purpose:** Creates a new sub-brand within the tenant.
*   **Required Role:** `tenant:admin`
*   **Request Body:** `{ "name": "Albaik Express", "logo_media_id": "..." }`
*   **Status Codes:** 201 Created.

## 32. Branch API Design

### List Branches
*   **Endpoint:** List Branches
*   **Method / Path:** `GET /v1/tenant/brands/{brand_id}/branches`
*   **Purpose:** Retrieves all physical locations for a brand.
*   **Access Level:** Any authenticated tenant user.
*   **Response Body:** `{ "data": [ { "id": "...", "name": "Jeddah Branch", "is_active": true } ] }`

### Create Branch
*   **Endpoint:** Create Branch
*   **Method / Path:** `POST /v1/tenant/brands/{brand_id}/branches`
*   **Required Role:** `branch:create`
*   **Request Body:** `{ "name": "Riyadh Branch", "timezone": "Asia/Riyadh", "coordinates": { "lat": 24.71, "lng": 46.67 } }`
*   **Status Codes:** 201 Created.

### Update Branch
*   **Endpoint:** Update Branch
*   **Method / Path:** `PATCH /v1/tenant/branches/{branch_id}`
*   **Required Role:** `branch:update`
*   **Request Body:** `{ "is_active": false }`
*   **Status Codes:** 200 OK.

## 46. Role & Permission API Design

### List Roles
*   **Endpoint:** List Roles
*   **Method / Path:** `GET /v1/tenant/roles`
*   **Purpose:** Lists default system roles and custom tenant roles.
*   **Access Level:** `role:read`
*   **Response Body:** `{ "data": [ { "id": "...", "name": "Manager", "is_system": true } ] }`

### Create Custom Role
*   **Endpoint:** Create Role
*   **Method / Path:** `POST /v1/tenant/roles`
*   **Required Role:** `tenant:admin`
*   **Request Body:** `{ "name": "Weekend Staff", "permission_codes": ["menu:read", "order:write"] }`

## 47. Settings API Design

### Get Settings
*   **Endpoint:** Get Tenant Settings
*   **Method / Path:** `GET /v1/tenant/settings`
*   **Access Level:** `settings:read`
*   **Response Body:** `{ "data": { "currency_code": "SAR", "tax_rate": 0.15, "features": {} } }`

### Update Settings
*   **Endpoint:** Update Tenant Settings
*   **Method / Path:** `PATCH /v1/tenant/settings`
*   **Required Role:** `tenant:admin`
*   **Request Body:** `{ "tax_rate": 0.05 }`
*   **Security Notes:** Updating financial settings logs a critical Audit event automatically.

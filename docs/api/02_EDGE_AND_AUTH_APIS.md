# API ENGINEERING: EDGE & AUTHENTICATION APIs

## 27. Public Menu API Design (Edge APIs)

### Get Public Menu
*   **Endpoint Name:** Get Public Menu
*   **HTTP Method:** `GET`
*   **URL Path:** `/v1/edge/menus/{tenant_slug}`
*   **Purpose:** Fetches the fully hydrated, hyper-optimized menu payload for a tenant.
*   **Access Level:** Public (Diners)
*   **Required Role:** None
*   **Tenant Rule:** Resolved via `{tenant_slug}` by API Gateway mapping.
*   **Authentication Required:** No
*   **Request Parameters:** `?branch_id=uuid` (optional), `?lang=ar-SA` (optional)
*   **Request Body:** None
*   **Response Body:** JSON object containing `theme`, `categories`, `products`, `modifiers`.
*   **Success Status Codes:** `200 OK`, `304 Not Modified`
*   **Error Status Codes:** `404 Not Found`, `429 Too Many Requests`
*   **Validation Rules:** `tenant_slug` must be a valid alphanumeric string.
*   **Rate Limit Rule:** 1,000 req/min per IP (Edge protection).
*   **Cache Rule:** `Cache-Control: public, s-maxage=3600, stale-while-revalidate=86400`. Supports ETag.
*   **Security Notes:** Payload MUST NOT contain internal dashboard IDs or unpublished items.
*   **Example Request:** `GET /v1/edge/menus/albaik?lang=en`
*   **Example Response:** `{"data": {"categories": [{"id": "...", "name": "Broast", "items": [...]}]}}`

### Search Public Menu
*   **Endpoint Name:** Search Public Menu
*   **HTTP Method:** `GET`
*   **URL Path:** `/v1/edge/menus/{tenant_slug}/search`
*   **Purpose:** Fuzzy search across items within a public menu.
*   **Access Level:** Public
*   **Required Role:** None
*   **Tenant Rule:** Resolved via `{tenant_slug}`.
*   **Authentication Required:** No
*   **Request Parameters:** `?q=burger` (required)
*   **Request Body:** None
*   **Response Body:** JSON array of matching `products`.
*   **Success Status Codes:** `200 OK`
*   **Error Status Codes:** `400 Bad Request` (Missing `q`), `404 Not Found`, `429 Too Many Requests`
*   **Validation Rules:** `q` length > 2.
*   **Rate Limit Rule:** 100 req/min per IP.
*   **Cache Rule:** Short-lived cache `s-maxage=60` to protect Elasticsearch.
*   **Security Notes:** Queries must be sanitized to prevent injection into search engine.
*   **Example Request:** `GET /v1/edge/menus/albaik/search?q=truffle`
*   **Example Response:** `{"data": [{"id": "...", "name": "Truffle Burger", "price": 15.00}]}`

## 30. Auth API Design

### Register User (Owner)
*   **Endpoint Name:** Register Tenant Owner
*   **HTTP Method:** `POST`
*   **URL Path:** `/v1/auth/register`
*   **Purpose:** Creates a new User and Tenant workspace simultaneously.
*   **Access Level:** Public (B2B Leads)
*   **Required Role:** None
*   **Tenant Rule:** N/A (Creates new tenant).
*   **Authentication Required:** No
*   **Request Parameters:** None
*   **Request Body:** `{"email": "...", "password": "...", "restaurant_name": "..."}`
*   **Response Body:** JSON object containing `user_id` and `tenant_id`.
*   **Success Status Codes:** `201 Created`
*   **Error Status Codes:** `400 Bad Request`, `409 Conflict` (Email taken), `422 Unprocessable Entity`
*   **Validation Rules:** Valid email format, password > 8 chars, strong complexity.
*   **Rate Limit Rule:** 3 req/hour per IP (Strict bot protection).
*   **Cache Rule:** No-Store.
*   **Security Notes:** Passwords hashed via Argon2 before DB insert.
*   **Example Request:** `POST /v1/auth/register {"email": "tariq@test.com", "password": "StrongPassword123!", "restaurant_name": "Albaik"}`
*   **Example Response:** `{"data": {"user_id": "...", "tenant_id": "..."}}`

### Login
*   **Endpoint Name:** Authenticate User
*   **HTTP Method:** `POST`
*   **URL Path:** `/v1/auth/login`
*   **Purpose:** Verifies credentials and issues JWTs.
*   **Access Level:** Public
*   **Required Role:** None
*   **Tenant Rule:** Global resolution.
*   **Authentication Required:** No
*   **Request Parameters:** None
*   **Request Body:** `{"email": "...", "password": "..."}`
*   **Response Body:** JSON object with `access_token` and `expires_in`. Cookie Header for refresh token.
*   **Success Status Codes:** `200 OK`
*   **Error Status Codes:** `401 Unauthorized`
*   **Validation Rules:** Valid email format.
*   **Rate Limit Rule:** 5 req/min per IP (Brute-force protection).
*   **Cache Rule:** No-Store.
*   **Security Notes:** Response sets `HttpOnly; Secure; SameSite=Strict` cookie for refresh token.
*   **Example Request:** `POST /v1/auth/login {"email": "tariq@test.com", "password": "..."}`
*   **Example Response:** Headers: `Set-Cookie: refresh_token=...` Body: `{"access_token": "eyJ...", "expires_in": 900}`

### Refresh Token
*   **Endpoint Name:** Refresh Access Token
*   **HTTP Method:** `POST`
*   **URL Path:** `/v1/auth/refresh`
*   **Purpose:** Issues a new Access Token using the HttpOnly refresh token cookie.
*   **Access Level:** Public (Relies on Cookie)
*   **Required Role:** None
*   **Tenant Rule:** Resolves from Refresh Token DB mapping.
*   **Authentication Required:** No (Implicit via Cookie)
*   **Request Parameters:** None
*   **Request Body:** None
*   **Response Body:** JSON object with new `access_token`.
*   **Success Status Codes:** `200 OK`
*   **Error Status Codes:** `401 Unauthorized` (Invalid/Expired cookie).
*   **Validation Rules:** Cookie must exist and match DB hash.
*   **Rate Limit Rule:** 10 req/min per IP.
*   **Cache Rule:** No-Store.
*   **Security Notes:** Cookie stealing mitigation via refresh token rotation (invaliding previous tokens).
*   **Example Request:** `POST /v1/auth/refresh` (Cookie attached automatically by browser).
*   **Example Response:** `{"access_token": "eyJ...", "expires_in": 900}`

### Logout
*   **Endpoint Name:** Logout User
*   **HTTP Method:** `POST`
*   **URL Path:** `/v1/auth/logout`
*   **Purpose:** Invalidates the refresh token and clears the cookie.
*   **Access Level:** Authenticated
*   **Required Role:** None
*   **Tenant Rule:** Resolves from JWT.
*   **Authentication Required:** Yes
*   **Request Parameters:** None
*   **Request Body:** None
*   **Response Body:** Empty.
*   **Success Status Codes:** `204 No Content`
*   **Error Status Codes:** `401 Unauthorized`
*   **Validation Rules:** Valid JWT required.
*   **Rate Limit Rule:** Standard API limit.
*   **Cache Rule:** No-Store.
*   **Security Notes:** Clears the HttpOnly cookie.
*   **Example Request:** `POST /v1/auth/logout`
*   **Example Response:** Header: `Set-Cookie: refresh_token=; Max-Age=0`

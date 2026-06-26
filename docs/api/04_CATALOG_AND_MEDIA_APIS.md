# API ENGINEERING: CATALOG & MEDIA APIs

## 33. Menu API Design

### List Menus
*   **Endpoint:** List Dashboard Menus
*   **Method / Path:** `GET /v1/tenant/brands/{brand_id}/menus`
*   **Purpose:** Lists all menus (draft and published) for a brand.
*   **Required Role:** `menu:read`

### Create Menu
*   **Endpoint:** Create Menu
*   **Method / Path:** `POST /v1/tenant/brands/{brand_id}/menus`
*   **Required Role:** `menu:write`
*   **Request Body:** `{ "internal_name": "Summer Menu" }`
*   **Status Codes:** 201 Created.

### Publish Menu
*   **Endpoint:** Publish Menu
*   **Method / Path:** `POST /v1/tenant/menus/{menu_id}/publish`
*   **Purpose:** Pushes a draft menu to the Edge CDN.
*   **Required Role:** `menu:publish`
*   **Status Codes:** 202 Accepted (Async publish process started).

## 34-35. Category & Product API Design

### Create Category
*   **Endpoint:** Create Category
*   **Method / Path:** `POST /v1/tenant/menus/{menu_id}/categories`
*   **Required Role:** `menu:write`
*   **Request Body:** `{ "internal_name": "Starters" }`

### Create Product
*   **Endpoint:** Create Product
*   **Method / Path:** `POST /v1/tenant/categories/{category_id}/products`
*   **Required Role:** `menu:write`
*   **Request Body:** `{ "base_price": 15.00, "dietary_tags": ["VEGAN"] }`
*   **Status Codes:** 201 Created.

### Quick Toggle Availability (The "86" Button)
*   **Endpoint:** Toggle Product Availability
*   **Method / Path:** `POST /v1/tenant/products/{product_id}/toggle-availability`
*   **Purpose:** High-speed endpoint for waitstaff to mark items Out of Stock.
*   **Required Role:** `staff:floor` (Lower permission than `menu:write`).
*   **Request Body:** `{ "is_available": false }`
*   **Performance:** Triggers immediate SSE WebSocket broadcast to diners.

## 36-37. Variant & Modifier API Design
*   **Endpoints:** `POST /v1/tenant/products/{product_id}/variants`, `POST /v1/tenant/modifier-groups`
*   **Structure:** Standard REST CRUD.

## 38. Media API Design
*Media uploads strictly follow the Pre-signed URL pattern to prevent API bottlenecks.*

### Request Upload URL
*   **Endpoint:** Get Presigned Upload URL
*   **Method / Path:** `POST /v1/tenant/media/upload-url`
*   **Purpose:** Requests an S3 pre-signed URL to upload a file directly from the browser.
*   **Required Role:** `media:write`
*   **Request Body:** `{ "file_name": "burger.jpg", "mime_type": "image/jpeg", "size_bytes": 1048576 }`
*   **Response Body:** `{ "data": { "upload_url": "https://s3.aws.com/...", "media_id": "..." } }`
*   **Security Notes:** Validates MIME type and size limits before issuing the URL.

### Confirm Upload
*   **Endpoint:** Confirm Media Upload
*   **Method / Path:** `POST /v1/tenant/media/{media_id}/confirm`
*   **Purpose:** Signals to the backend that the client finished the S3 upload. Triggers the async image processing pipeline (WebP/Blurhash).

## 39. Theme API Design
*   **Endpoint:** Update Brand Theme
*   **Method / Path:** `PUT /v1/tenant/brands/{brand_id}/theme`
*   **Request Body:** `{ "primary_color": "#FF0000", "font_family": "Inter", "layout": "GRID" }`

## 40. Localization API Design

### Upsert Translations
*   **Endpoint:** Bulk Update Translations
*   **Method / Path:** `PUT /v1/tenant/translations`
*   **Purpose:** Updates the dictionary strings for various entities.
*   **Request Body:**
    ```json
    {
      "language": "ar-SA",
      "translations": [
        { "entity_type": "PRODUCT", "entity_id": "...", "field": "name", "value": "برجر ترافل" }
      ]
    }
    ```

## 41. QR Code API Design

### Generate Branch QR
*   **Endpoint:** Generate QR
*   **Method / Path:** `POST /v1/tenant/branches/{branch_id}/qr`
*   **Purpose:** Creates a dynamic QR routing rule and generates the visual asset.
*   **Request Body:** `{ "table_number": "12" }`
*   **Response Body:** `{ "data": { "short_url": "sm.to/XyZ", "svg_download_url": "..." } }`

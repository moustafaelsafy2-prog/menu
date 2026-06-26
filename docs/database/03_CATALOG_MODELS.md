# DATABASE ENGINEERING: CATALOG MODELS

*Note: ALL tables here include a `tenant_id` column for RLS.*

## 8. Menu Model
*   **Table Name:** `menus`
*   **Purpose:** The top-level container for categories.
*   **Columns:**
    *   `id` | `UUID` | Not Null | Default: `gen_random_uuid()`
    *   `tenant_id` | `UUID` | Not Null | Default: None
    *   `brand_id` | `UUID` | Not Null | Default: None
    *   `internal_name` | `VARCHAR(255)` | Not Null | Default: None
    *   `is_published` | `BOOLEAN` | Not Null | Default: `FALSE`
    *   `schedule_json` | `JSONB` | Nullable | Default: `NULL`
    *   `created_at`, `updated_at`, `deleted_at` | `TIMESTAMPTZ`
*   **Primary Key:** `(tenant_id, id)`
*   **Foreign Keys:** `FOREIGN KEY (tenant_id, brand_id) REFERENCES brands(tenant_id, id)`
*   **Unique Constraints:** None
*   **Check Constraints:** None
*   **Indexes:** `idx_menus_brand_id`
*   **Relations:** `M:1` with `brands`, `1:M` with `categories`
*   **Example Row:** `id: '...', tenant_id: '...', brand_id: '...', internal_name: 'Summer Menu', is_published: true`
*   **Expected Volume:** 500,000 rows
*   **Read/Write Frequency:** Low write / High read
*   **Archival Strategy:** Retained indefinitely.
*   **Security Considerations:** Must not expose `internal_name` to public API.
*   **Tenant Isolation Rule:** RLS enforced via `tenant_id`.

## 9. Category Model
*   **Table Name:** `categories`
*   **Purpose:** Menu structural taxonomy.
*   **Columns:**
    *   `id` | `UUID` | Not Null | Default: `gen_random_uuid()`
    *   `tenant_id` | `UUID` | Not Null | Default: None
    *   `menu_id` | `UUID` | Not Null | Default: None
    *   `parent_category_id` | `UUID` | Nullable | Default: `NULL`
    *   `sort_order` | `INTEGER` | Not Null | Default: `0`
    *   `media_id` | `UUID` | Nullable | Default: `NULL`
    *   `created_at`, `updated_at`, `deleted_at` | `TIMESTAMPTZ`
*   **Primary Key:** `(tenant_id, id)`
*   **Foreign Keys:** `FOREIGN KEY (tenant_id, menu_id) REFERENCES menus(tenant_id, id)`
*   **Unique Constraints:** None
*   **Check Constraints:** None
*   **Indexes:** `idx_categories_menu_id`
*   **Relations:** `M:1` with `menus`, `1:M` with `products`
*   **Example Row:** `id: '...', menu_id: '...', sort_order: 1`
*   **Expected Volume:** 5,000,000 rows
*   **Read/Write Frequency:** Low write / High read
*   **Archival Strategy:** Retained indefinitely.
*   **Security Considerations:** N/A
*   **Tenant Isolation Rule:** RLS enforced via `tenant_id`.

## 10. Product Model
*   **Table Name:** `products`
*   **Purpose:** The canonical entity for a dish.
*   **Columns:**
    *   `id` | `UUID` | Not Null | Default: `gen_random_uuid()`
    *   `tenant_id` | `UUID` | Not Null | Default: None
    *   `category_id` | `UUID` | Not Null | Default: None
    *   `base_price` | `NUMERIC(10,2)` | Not Null | Default: `0.00`
    *   `calories` | `INTEGER` | Nullable | Default: `NULL`
    *   `is_available` | `BOOLEAN` | Not Null | Default: `TRUE`
    *   `sort_order` | `INTEGER` | Not Null | Default: `0`
    *   `media_gallery_json` | `JSONB` | Nullable | Default: `NULL`
    *   `dietary_tags` | `VARCHAR[]` | Nullable | Default: `NULL`
    *   `allergens` | `VARCHAR[]` | Nullable | Default: `NULL`
    *   `created_at`, `updated_at`, `deleted_at` | `TIMESTAMPTZ`
*   **Primary Key:** `(tenant_id, id)`
*   **Foreign Keys:** `FOREIGN KEY (tenant_id, category_id) REFERENCES categories(tenant_id, id)`
*   **Unique Constraints:** None
*   **Check Constraints:** `CHECK (base_price >= 0)`
*   **Indexes:** `idx_products_category_id`, GIN on `dietary_tags`
*   **Relations:** `M:1` with `categories`
*   **Example Row:** `id: '...', base_price: 15.00, is_available: true, dietary_tags: ['VEGAN']`
*   **Expected Volume:** 50,000,000 rows
*   **Read/Write Frequency:** Medium write / Very High read
*   **Archival Strategy:** Retained indefinitely.
*   **Security Considerations:** Price updates trigger audit logs.
*   **Tenant Isolation Rule:** RLS enforced via `tenant_id`.

## 11. Product Variant Model
*   **Table Name:** `product_variants`
*   **Purpose:** Variations that change the item (e.g., Size).
*   **Columns:**
    *   `id` | `UUID` | Not Null | Default: `gen_random_uuid()`
    *   `tenant_id` | `UUID` | Not Null | Default: None
    *   `product_id` | `UUID` | Not Null | Default: None
    *   `sku` | `VARCHAR(100)` | Nullable | Default: `NULL`
    *   `price_adjustment` | `NUMERIC(10,2)` | Not Null | Default: `0.00`
    *   `created_at`, `updated_at`, `deleted_at` | `TIMESTAMPTZ`
*   **Primary Key:** `(tenant_id, id)`
*   **Foreign Keys:** `FOREIGN KEY (tenant_id, product_id) REFERENCES products(tenant_id, id)`
*   **Unique Constraints:** None
*   **Check Constraints:** None
*   **Indexes:** `idx_variants_product_id`
*   **Relations:** `M:1` with `products`
*   **Example Row:** `product_id: '...', sku: 'BRG-LRG', price_adjustment: 5.00`
*   **Expected Volume:** 20,000,000 rows
*   **Read/Write Frequency:** Low write / High read
*   **Archival Strategy:** Retained indefinitely.
*   **Security Considerations:** None.
*   **Tenant Isolation Rule:** RLS enforced via `tenant_id`.

## 12. Modifier & Option Model
*   **Table Name:** `modifier_groups`
*   **Purpose:** "Choose Your Side".
*   **Columns:**
    *   `id` | `UUID` | Not Null | Default: `gen_random_uuid()`
    *   `tenant_id` | `UUID` | Not Null | Default: None
    *   `internal_name` | `VARCHAR(255)` | Not Null | Default: None
    *   `min_selections` | `INTEGER` | Not Null | Default: `0`
    *   `max_selections` | `INTEGER` | Nullable | Default: `NULL`
    *   `created_at`, `updated_at`, `deleted_at` | `TIMESTAMPTZ`
*   **Primary Key:** `(tenant_id, id)`
*   **Foreign Keys:** None
*   **Unique Constraints:** None
*   **Check Constraints:** `CHECK (min_selections >= 0)`
*   **Indexes:** None
*   **Relations:** `M:N` with `products`
*   **Example Row:** `internal_name: 'Meat Temp', min_selections: 1, max_selections: 1`
*   **Expected Volume:** 10,000,000 rows
*   **Read/Write Frequency:** Low write / High read
*   **Archival Strategy:** Retained indefinitely.
*   **Security Considerations:** None
*   **Tenant Isolation Rule:** RLS enforced via `tenant_id`.

## 13. Media Library Model
*   **Table Name:** `media`
*   **Purpose:** Centralized repository for images/videos.
*   **Columns:**
    *   `id` | `UUID` | Not Null | Default: `gen_random_uuid()`
    *   `tenant_id` | `UUID` | Not Null | Default: None
    *   `file_name` | `VARCHAR(255)` | Not Null | Default: None
    *   `mime_type` | `VARCHAR(100)` | Not Null | Default: None
    *   `size_bytes` | `BIGINT` | Not Null | Default: `0`
    *   `cdn_url_original` | `VARCHAR(1024)` | Not Null | Default: None
    *   `cdn_url_webp` | `VARCHAR(1024)` | Nullable | Default: `NULL`
    *   `blurhash` | `VARCHAR(100)` | Nullable | Default: `NULL`
    *   `created_at`, `updated_at`, `deleted_at` | `TIMESTAMPTZ`
*   **Primary Key:** `(tenant_id, id)`
*   **Foreign Keys:** None
*   **Unique Constraints:** None
*   **Check Constraints:** None
*   **Indexes:** None
*   **Relations:** Referenced by `categories`, `products`, `brands`
*   **Example Row:** `file_name: 'burger.jpg', mime_type: 'image/jpeg', cdn_url_webp: '...'`
*   **Expected Volume:** 50,000,000 rows
*   **Read/Write Frequency:** High write (uploads) / High read
*   **Archival Strategy:** Soft-deleted media is physically deleted from S3 after 30 days.
*   **Security Considerations:** Must validate MIME types against malicious uploads.
*   **Tenant Isolation Rule:** RLS enforced via `tenant_id`.

## 14. Theme Model
*   **Table Name:** `themes`
*   **Purpose:** Custom CSS and layouts.
*   **Columns:**
    *   `id` | `UUID` | Not Null | Default: `gen_random_uuid()`
    *   `tenant_id` | `UUID` | Not Null | Default: None
    *   `brand_id` | `UUID` | Not Null | Default: None
    *   `primary_color` | `VARCHAR(7)` | Not Null | Default: `'#000000'`
    *   `font_family` | `VARCHAR(100)` | Not Null | Default: `'Inter'`
    *   `layout_type` | `VARCHAR(50)` | Not Null | Default: `'GRID'`
    *   `custom_css` | `TEXT` | Nullable | Default: `NULL`
    *   `created_at`, `updated_at` | `TIMESTAMPTZ`
*   **Primary Key:** `(tenant_id, id)`
*   **Foreign Keys:** `FOREIGN KEY (tenant_id, brand_id) REFERENCES brands(tenant_id, id)`
*   **Unique Constraints:** `UNIQUE(tenant_id, brand_id)`
*   **Check Constraints:** None
*   **Indexes:** None
*   **Relations:** `1:1` with `brands`
*   **Example Row:** `primary_color: '#FF0000', layout_type: 'GRID'`
*   **Expected Volume:** 150,000 rows
*   **Read/Write Frequency:** Low write / High read
*   **Archival Strategy:** Retained indefinitely.
*   **Security Considerations:** `custom_css` MUST be sanitized to prevent XSS.
*   **Tenant Isolation Rule:** RLS enforced via `tenant_id`.

## 15. Localization Model
*   **Table Name:** `translations`
*   **Purpose:** The central dictionary for text fields.
*   **Columns:**
    *   `tenant_id` | `UUID` | Not Null | Default: None
    *   `entity_type` | `VARCHAR(50)` | Not Null | Default: None
    *   `entity_id` | `UUID` | Not Null | Default: None
    *   `language_code` | `VARCHAR(5)` | Not Null | Default: None
    *   `field_name` | `VARCHAR(100)` | Not Null | Default: None
    *   `translation_value` | `TEXT` | Not Null | Default: None
*   **Primary Key:** `(tenant_id, entity_type, entity_id, language_code, field_name)`
*   **Foreign Keys:** None (Polymorphic association)
*   **Unique Constraints:** (Covered by PK)
*   **Check Constraints:** None
*   **Indexes:** None
*   **Relations:** Polymorphic
*   **Example Row:** `entity_type: 'PRODUCT', language_code: 'ar-SA', field_name: 'name', value: 'برجر'`
*   **Expected Volume:** 500,000,000 rows
*   **Read/Write Frequency:** Medium write / Very High read
*   **Archival Strategy:** Retained indefinitely.
*   **Security Considerations:** None.
*   **Tenant Isolation Rule:** RLS enforced via `tenant_id`.

## 16. QR Code Model
*   **Table Name:** `qr_codes`
*   **Purpose:** Manages routing hashes and assets.
*   **Columns:**
    *   `id` | `UUID` | Not Null | Default: `gen_random_uuid()`
    *   `tenant_id` | `UUID` | Not Null | Default: None
    *   `branch_id` | `UUID` | Not Null | Default: None
    *   `table_number` | `VARCHAR(50)` | Nullable | Default: `NULL`
    *   `short_hash` | `VARCHAR(10)` | Not Null | Default: None
    *   `svg_url` | `VARCHAR(1024)` | Nullable | Default: `NULL`
    *   `scans_count` | `BIGINT` | Not Null | Default: `0`
    *   `created_at`, `updated_at`, `deleted_at` | `TIMESTAMPTZ`
*   **Primary Key:** `(tenant_id, id)`
*   **Foreign Keys:** `FOREIGN KEY (tenant_id, branch_id) REFERENCES branches(tenant_id, id)`
*   **Unique Constraints:** `UNIQUE(short_hash)`
*   **Check Constraints:** None
*   **Indexes:** `idx_qr_hash`
*   **Relations:** `M:1` with `branches`
*   **Example Row:** `table_number: '12', short_hash: 'XyZ12p', scans_count: 45`
*   **Expected Volume:** 5,000,000 rows
*   **Read/Write Frequency:** Low write / High read
*   **Archival Strategy:** Retained indefinitely.
*   **Security Considerations:** `short_hash` must be unguessable to prevent QR scraping.
*   **Tenant Isolation Rule:** RLS enforced via `tenant_id`.

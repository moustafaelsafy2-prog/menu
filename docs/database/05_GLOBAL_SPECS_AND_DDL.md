# DATABASE ENGINEERING: GLOBAL SPECIFICATIONS

## 27. Full ERD Description (Entity Relationship flow)
1.  **Tenant** (1:M) **Brands**
2.  **Brand** (1:M) **Branches**
3.  **Brand** (1:M) **Menus**
4.  **Menu** (1:M) **Categories**
5.  **Category** (1:M) **Products**
6.  **Product** (1:M) **Product_Variants**
7.  **Product** (M:N) **Modifier_Groups**
8.  **Modifier_Group** (1:M) **Modifier_Options**
9.  **Tenant** (1:M) **Media** (referenced by Categories and Products)
10. **Branch** (1:M) **QR_Codes**

## 30. Data Types Standardization
*   **IDs:** `UUID` (Specifically UUIDv7 generated in app layer or DB extension).
*   **Monetary Values:** `NUMERIC(10,2)` (Better than standard DECIMAL/FLOAT to prevent floating point rounding errors in Postgres).
*   **Dates:** `TIMESTAMPTZ` (Timestamp with Time Zone. Essential for a multi-branch system across GCC timezones).
*   **Strings:** `VARCHAR(255)` for names, `TEXT` for descriptions.
*   **Booleans:** `BOOLEAN` with strict `DEFAULT FALSE/TRUE`.
*   **Sparse/Flexible Data:** `JSONB` (Not JSON. JSONB is binary, indexable, and faster to query).

## 31-34. Keys & Constraints Strategy
*   **Primary Keys:** Always `id UUID`. For tables heavily partitioned by tenant, PK becomes `(tenant_id, id)`.
*   **Foreign Keys:** Enforced at the database level. `ON DELETE CASCADE` is strictly forbidden for core entities (Products, Categories) to prevent catastrophic data loss. Use `ON DELETE RESTRICT` and enforce soft-deletes via application logic.
*   **Unique Constraints:** Applied to slugs (`slug`), emails (`email`), and integration mappings (`tenant_id, pos_sku`).
*   **Check Constraints:** `CONSTRAINT chk_price_positive CHECK (base_price >= 0)`.

## 35-38. Indexing Strategy
*   **B-Tree Indexes:** Applied automatically to all PKs and FKs. Applied manually to high-cardinality search fields (e.g., `email`).
*   **Composite Indexes:** Used for frequent multi-column lookups: `CREATE INDEX idx_tenant_status ON tenants(tenant_id, status);`
*   **Partial Indexes:** Excellent for soft-deleted tables: `CREATE INDEX idx_active_products ON products(tenant_id) WHERE deleted_at IS NULL;`
*   **GIN Indexes:** Used for `JSONB` columns (`features_json`) and Array columns (`dietary_tags`).

## 59. Recommended Prisma Schema Structure
If using Prisma ORM, the `tenant_id` must be explicitly defined in every model to facilitate Prisma Client Extensions for automatic RLS injection.

```prisma
model Tenant {
  id        String    @id @db.Uuid
  name      String    @db.VarChar(255)
  brands    Brand[]
  createdAt DateTime  @default(now()) @map("created_at")

  @@map("tenants")
}

model Product {
  id         String    @id @db.Uuid
  tenantId   String    @map("tenant_id") @db.Uuid
  categoryId String    @map("category_id") @db.Uuid
  basePrice  Decimal   @map("base_price") @db.Decimal(10,2)
  deletedAt  DateTime? @map("deleted_at")

  category   Category  @relation(fields: [categoryId], references: [id])

  @@index([tenantId])
  @@index([categoryId])
  @@map("products")
}
```

## 60. Recommended SQL DDL Structure (The "Golden Setup")
Every table creation should follow this robust pattern:

```sql
CREATE TABLE public.products (
    id UUID NOT NULL DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL,
    category_id UUID NOT NULL,
    base_price NUMERIC(10,2) NOT NULL,
    is_available BOOLEAN NOT NULL DEFAULT TRUE,
    created_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP,
    deleted_at TIMESTAMPTZ,

    CONSTRAINT pk_products PRIMARY KEY (tenant_id, id),
    CONSTRAINT fk_products_tenant FOREIGN KEY (tenant_id) REFERENCES tenants(id) ON DELETE RESTRICT,
    CONSTRAINT fk_products_category FOREIGN KEY (tenant_id, category_id) REFERENCES categories(tenant_id, id) ON DELETE RESTRICT,
    CONSTRAINT chk_products_price CHECK (base_price >= 0)
);

-- Performance Indexes
CREATE INDEX idx_products_category ON products(tenant_id, category_id) WHERE deleted_at IS NULL;

-- Multi-Tenant RLS Security
ALTER TABLE products ENABLE ROW LEVEL SECURITY;
CREATE POLICY isolation_policy ON products
    USING (tenant_id = current_setting('app.current_tenant_id')::UUID);

-- Audit Trigger
CREATE TRIGGER trg_audit_products
    AFTER INSERT OR UPDATE OR DELETE ON products
    FOR EACH ROW EXECUTE FUNCTION fn_audit_logger();
```

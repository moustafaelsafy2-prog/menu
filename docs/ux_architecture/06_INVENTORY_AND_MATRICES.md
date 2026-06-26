# UX ARCHITECTURE: INVENTORY & MATRICES

## 1. Master Screen Inventory
A comprehensive list of every distinct view requiring design.

**Public Customer (Edge PWA):**
1.  QR Router (Loading)
2.  Menu Home (Category List)
3.  Product Details (Bottom Sheet)
4.  Search Overlay
5.  Language Selector (Bottom Sheet)
6.  Offline State / 404 / 500

**Restaurant Dashboard (B2B SPA):**
7.  Auth: Login, Register, Forgot Password, Reset Password
8.  Home (Analytics Overview)
9.  Products List
10. Product Editor (Drawer)
11. Categories List (Draggable)
12. Modifiers List
13. Modifier Editor (Drawer)
14. Media Library (Grid)
15. Theme Editor (Split View)
16. Branch Manager
17. QR Generator & Downloader
18. Users List
19. Roles & Permissions List
20. Subscription & Billing
21. Audit Logs (Data Table)
22. Tenant Settings

**Super Admin:**
23. Global Dashboard
24. Tenants List
25. Tenant Detail View

## 2. Screen Dependency Map
*If X changes, what screens must update?*
*   **Media Library:** Dependencies -> Product Editor (Image selection), Theme Editor (Logo selection), Public Menu (CDN delivery).
*   **Theme Editor:** Dependencies -> Public Menu (Colors/Fonts update globally).
*   **Categories List:** Dependencies -> Products List (Category Dropdown), Public Menu (Category Navigation).

## 3. Navigation Graph
```
[Login] --> [Dashboard Home]
                |--> [Products] ---> [Edit Product Drawer]
                |--> [Theme]    ---> [Live Preview iframe]
                |--> [Billing]  ---> [Stripe Hosted Checkout]
                |--> [Settings]
```

## 4. Feature Dependency Matrix
| Feature | Depends On | Blocks |
| :--- | :--- | :--- |
| **QR Generator** | Branches | Diner Acquisition |
| **Products List** | Categories | Menu Publishing |
| **Theme Editor** | Media (Logo) | Public Menu Branding |
| **Billing** | Tenant Auth | SaaS Revenue |

## 5. Release Priority, Complexity, & Business Value

| Feature / Screen | Priority | Complexity (1-10) | Business Value (1-10) | Notes |
| :--- | :--- | :--- | :--- | :--- |
| **Public Menu Home** | P0 (MVP) | 8 | 10 | The core product. Must be flawless and fast. |
| **Dashboard Catalog** | P0 (MVP) | 7 | 10 | Required for data entry. |
| **QR Generator** | P0 (MVP) | 5 | 9 | The physical bridge to the digital product. |
| **Auth & Settings** | P0 (MVP) | 4 | 8 | Foundational. |
| **Theme Editor** | P1 | 6 | 9 | High perceived value for premium restaurants. |
| **Dashboard Analytics**| P1 | 7 | 8 | Required for retention and proving ROI. |
| **Media Library** | P1 | 6 | 7 | Could use simple file inputs for MVP, central library is P1. |
| **Audit Logs** | P2 | 4 | 5 | Enterprise requirement, not needed for small cafes. |
| **Custom Roles (RBAC)**| P2 | 8 | 6 | High complexity due to permission checks. |
| **Future Ordering** | P3 | 10 | 10 | Massive revenue driver, but fundamentally changes the business model from SaaS to Fintech. |

## 6. UX Architectural Summary
The SmartMenu UX Architecture deliberately separates the structural complexity of the **Dashboard** (utilitarian, dense, B2B) from the emotional simplicity of the **Public Menu** (luxurious, fast, B2C). By strictly defining these 25 screens, their entry/exit points, and their state machines prior to visual design, we ensure that the engineering and design teams have a flawless, shared blueprint that eliminates assumptions and accelerates the implementation phase.

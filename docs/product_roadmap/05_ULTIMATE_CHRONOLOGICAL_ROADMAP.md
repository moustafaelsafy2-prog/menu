# PRODUCT ROADMAP: THE ULTIMATE CHRONOLOGICAL BUILD SEQUENCE

*This document outlines the exact sequence in which code should be written and features released to minimize rework, validate assumptions early, and maintain a steady release cadence.*

## PHASE 1: THE FOUNDATION (Months 1-2)
*The goal is to get a single menu online. Nothing else matters.*
1.  **Core DB & Schema Setup:** Tenants, Brands, Menus, Categories, Products.
2.  **Basic Auth:** Registration, Login, JWT generation.
3.  **Tenant Setup Wizard:** Create brand, set timezone, set currency.
4.  **Catalog CRUD API:** Endpoints to create/edit Categories and Products.
5.  **B2B Dashboard Skeleton:** Sidebar navigation, topbar.
6.  **Catalog UI:** Build the Products Data Table and the Create Product Drawer.
7.  **Media Upload Pipeline:** S3 presigned URLs and basic image display.
8.  **Edge Routing Logic:** Map `slug` to `tenant_id`.
9.  **Public Menu UI (Mobile):** Build the actual diner-facing menu based on the DB schema.
10. **QR Generator Service:** Generate a physical link to the public menu.

*Milestone: A restaurant owner can manually enter 10 items, print a QR code, and a diner can view the menu on their phone.*

## PHASE 2: THE COMMERCIAL LAUNCH (Month 3)
*The goal is to charge money for the product.*
11. **Stripe Billing Integration:** Create Customer, Create Subscription.
12. **Subscription Middleware:** Lock the dashboard if the trial expires.
13. **Theme Engine:** Allow owners to pick a primary color and font.
14. **Public Menu Theming:** Connect the Edge UI to the Theme Engine variables.
15. **Dietary & Allergen Tags:** Add array fields to Products and badges to the Public Menu.
16. **Basic Analytics Ingestion:** Track `menu_view` and `item_view`.
17. **Dashboard Home UI:** Build the KPI cards showing views.

*Milestone: The platform is a viable SaaS product generating MRR.*

## PHASE 3: THE ENTERPRISE UPGRADE (Months 4-6)
*The goal is to capture chains and multi-location businesses.*
18. **Branch Management:** Add physical locations, link QR codes to branches, not just brands.
19. **Variant Engine:** Support Sizes (Small/Medium/Large).
20. **Modifier Engine:** Support "Add-ons" and "Options". Complex recursive schema.
21. **Public Menu UI Update:** Build the Bottom Sheet to display Variants and Modifiers elegantly.
22. **RBAC Engine:** Users, Roles, Permissions tables and middleware.
23. **Team Management UI:** Allow owners to invite staff via email.
24. **Localization Engine:** Translation tables, Arabic/English toggle on Public Menu.

*Milestone: SmartMenu can support a 50-location fast-food chain with complex modifier math and multi-lingual diners.*

## PHASE 4: THE OPERATIONS BRIDGE (Months 7-10)
*The goal is to stop restaurant staff from double-entering data.*
25. **The "86" Fast Toggle:** High-speed availability switch on Dashboard + Real-time SSE push to Public Menu.
26. **Menu Scheduling:** Breakfast vs Dinner menu automated visibility.
27. **POS Sync Framework:** Build the generic async worker queue.
28. **Foodics Integration:** One-way sync (Pull from Foodics -> Overwrite SmartMenu Catalog).
29. **Deliverect Integration:** (Same as above).
30. **Agency Dashboard:** Create the "Super-Tenant" view for marketing agencies.

*Milestone: SmartMenu is deeply embedded in the physical operations of the restaurant. Churn drops significantly.*

## PHASE 5: THE FINTECH PIVOT (Months 11-18)
*The goal is to process money.*
31. **Cart & Session State:** Allow Diners to add items to a local session cart.
32. **Checkout UI:** Build the checkout form on the Public Menu.
33. **Payment Gateway Integration:** Stripe Connect / Tap Payments API.
34. **Order Routing Engine:** Send the placed order to the Restaurant Dashboard.
35. **KDS (Kitchen Display System) UI:** Build the "Incoming Orders" real-time view for staff.
36. **POS Two-Way Sync:** Send the paid order directly into the Foodics POS.
37. **Apple Pay / Google Pay:** Native wallet integrations.

*Milestone: SmartMenu takes a % cut of the restaurant's gross merchandise value.*

## PHASE 6: THE ECOSYSTEM (Years 2-5)
*The goal is to own the hospitality network.*
38. **Bill Splitting Logic:** Complex cart division and multi-payment capture.
39. **Global Diner Profile:** Diners create an account to save cards/history across *all* SmartMenu restaurants.
40. **Coalition Loyalty Engine:** Earn points at Restaurant A, burn at Restaurant B.
41. **AI "Pairs Well With" Engine:** Machine learning recommendation models.
42. **Consumer App:** Launch the "SmartMenu Discovery" native app to drive traffic to partners.

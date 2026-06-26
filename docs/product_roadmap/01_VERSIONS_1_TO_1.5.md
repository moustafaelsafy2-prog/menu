# PRODUCT ROADMAP: VERSIONS 1.0 TO 1.5

## VERSION 1.0 (The Foundation MVP)
*   **Goals:** Launch the core "View-Only" digital menu platform. Prove the business model. Establish basic SaaS revenue.
*   **Business Value:** High (Establishes market presence and early MRR).
*   **Technical Complexity:** High (Requires setting up the complex multi-tenant architecture and Edge delivery).
*   **Estimated Development Effort:** 3 Months (Core engineering team).
*   **Dependencies:** Edge CDN configuration, Stripe integration, Core Database setup.
*   **Release Criteria:** 99.9% uptime, <150ms menu load time, zero tenant data leakage.
*   **KPIs:** MRR, Active Tenants, Menu Load Time, Activation Rate.
*   **Risks:** High churn if the QR generation or basic menu UI feels generic.
*   **Success Metrics:** 100 paying restaurants, 1M menu scans.

### Features
| Feature | Priority | User Impact | Revenue Impact | Difficulty | Future Dependencies |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Multi-Tenant Auth** | P0 | Low (Invisible) | Low | High | All B2B workflows |
| **Catalog Manager (CRUD)** | P0 | High | High | Med | V1.5 POS Integrations |
| **Edge-Rendered Menu** | P0 | Critical | High | High | V2.0 Ordering |
| **QR Code Generator** | P0 | High | Med | Low | V1.2 Advanced QRs |
| **Stripe Subscriptions** | P0 | Low | Critical| Med | V1.1 White-label Billing |

---

## VERSION 1.1 (The Localization & Polish Update)
*   **Goals:** Dominate the Middle Eastern market by providing native, flawless bilingual support and premium design customizations.
*   **Business Value:** High (Captures the luxury/fine-dining segment).
*   **Technical Complexity:** Medium.
*   **Estimated Development Effort:** 1.5 Months.
*   **Dependencies:** V1.0 Core.
*   **Release Criteria:** Zero UI layout shifts when toggling AR/EN.
*   **KPIs:** AR vs EN usage ratio, Upgrade rate to Pro tier.
*   **Risks:** Poor translation quality damages brand reputation.

### Features
| Feature | Priority | User Impact | Revenue Impact | Difficulty | Future Dependencies |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Deep RTL Support** | P0 | Critical | High | High | V3.0 AI Translations |
| **Theme Customizer** | P1 | High | Med | Med | V1.2 Agency Dashboard |
| **Dietary/Allergen Tags** | P1 | High | Low | Low | V2.5 Smart Filtering |

---

## VERSION 1.2 (The B2B Growth Engine)
*   **Goals:** Introduce features that allow SmartMenu to scale via partnerships (Agencies, Franchises) rather than just direct sales.
*   **Business Value:** Critical (Reduces CAC by utilizing partner networks).
*   **Technical Complexity:** Medium (Primarily RBAC and routing logic).
*   **Estimated Development Effort:** 2 Months.
*   **Dependencies:** V1.0 Auth.
*   **Release Criteria:** Agency accounts can successfully manage 10+ sub-tenants without re-authenticating.

### Features
| Feature | Priority | User Impact | Revenue Impact | Difficulty | Future Dependencies |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Agency Dashboard** | P0 | High (B2B) | Critical | High | V4.0 Whitelabel Platform |
| **Multi-Branch Sync** | P0 | High (B2B) | High | Med | V2.0 Location-based Pricing |
| **Advanced Analytics** | P1 | Med | Med | High | V3.0 AI Recommendations |

---

## VERSION 1.5 (The Operations Bridge)
*   **Goals:** Connect SmartMenu to the restaurant's existing physical operations to reduce double-data entry.
*   **Business Value:** Very High (Makes the platform 'sticky' and reduces churn to near-zero).
*   **Technical Complexity:** Very High (Dealing with chaotic 3rd party APIs).
*   **Estimated Development Effort:** 4 Months.
*   **Dependencies:** V1.2 Multi-branch.
*   **Release Criteria:** Live sync with Foodics without corrupting our internal catalog schema.
*   **Risks:** POS API rate limits or outages cause our platform to look broken.

### Features
| Feature | Priority | User Impact | Revenue Impact | Difficulty | Future Dependencies |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Foodics POS Sync** | P0 | High | Med | Very High | V2.0 Native Ordering |
| **Deliverect Sync** | P1 | High | Med | Very High | V2.0 Native Ordering |
| **The "86" Fast Toggle**| P1 | High | Low | Low | V2.0 Inventory Mapping |

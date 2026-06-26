# PRODUCT ROADMAP: STRATEGIC MATRICES

## 1. Feature Matrix (Value vs. Effort)
Used by the Product Team to prioritize sprint planning.

| Feature | Value Score (1-10) | Effort Score (1-10) | Quadrant | Action |
| :--- | :--- | :--- | :--- | :--- |
| **Edge-Rendered Menu** | 10 | 8 | High Value / High Effort | Strategic Bet (V1.0) |
| **QR Generator** | 8 | 2 | High Value / Low Effort | Quick Win (V1.0) |
| **POS Sync (Foodics)** | 9 | 9 | High Value / High Effort | Strategic Bet (V1.5) |
| **Dietary Tags** | 6 | 2 | Med Value / Low Effort | Quick Win (V1.1) |
| **Custom Fonts** | 4 | 6 | Low Value / High Effort | De-prioritize |
| **Split Bill Payments** | 10 | 9 | High Value / High Effort | Strategic Bet (V2.5) |

## 2. Release Matrix (Target vs. Actual)
Used to track delivery velocity and technical debt accumulation.

| Version | Theme | Target Date | Status | Tech Debt Accrued |
| :--- | :--- | :--- | :--- | :--- |
| **V1.0** | The Foundation | Month 3 | Planned | Minimal (Strict architecture followed) |
| **V1.1** | Localization | Month 4.5 | Planned | TBD |
| **V1.2** | Agency B2B | Month 6 | Planned | High risk of RBAC bloat |
| **V1.5** | POS Sync | Month 10 | Planned | High risk of mapping tech debt |
| **V2.0** | Payments | Month 16 | Planned | TBD |

## 3. Dependency Matrix
Critical path mapping for engineering and partnerships.

| Deliverable | Depends On (Internal) | Depends On (External) | Blocker Risk |
| :--- | :--- | :--- | :--- |
| **Public Menu** | Catalog API, Media API | Cloudflare/Vercel Edge | Low |
| **SaaS Billing** | Auth Service, Tenant Service | Stripe Billing API | Low |
| **Native Payments** | Order Routing Engine | Stripe Connect / Tap | Medium (Compliance/KYC) |
| **Foodics Sync** | Branch Manager | Foodics Open API | High (3rd party rate limits) |
| **AI Translations** | Localization DB | DeepL / OpenAI API | Low |
| **Push Notifications** | Global Diner Profile | Apple APNs / Google FCM | Medium |

## 4. Platform Evolution Matrix

| Era | Primary Buyer | Core Value Proposition | Revenue Model | Competitor Landscape |
| :--- | :--- | :--- | :--- | :--- |
| **V1.x (SaaS)** | Restaurant Manager | "Save money on printing." | Flat Monthly Fee | High (PDF QR Makers) |
| **V2.x (Fintech)** | Ops Director | "Turn tables 15% faster." | Flat Fee + Txn Take Rate | Low (Specialized POS players) |
| **V4.x (Network)** | Marketing Director | "Acquire new diners." | Txn Take Rate + Ad Spend | Big Tech (UberEats, Google) |

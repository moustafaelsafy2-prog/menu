# BUSINESS OS: MODEL & PRICING STRATEGY

## 1. Business Model
SmartMenu operates as a B2B SaaS platform with a long-term goal of transitioning into a B2B2C Fintech/Payments facilitator.
*   **Phase 1 (Years 1-2):** Predictable, recurring SaaS subscription revenue.
*   **Phase 2 (Years 2-5):** Transactional revenue (GMV take-rate) via native ordering and payments.

## 2. Pricing Strategy & Subscriptions
Pricing is value-based, anchored against the cost of printing physical menus and the revenue lost to slow table turns. Prices are localized to purchasing power parity (PPP) per region (e.g., UAE vs. Egypt).

### 2.1. Free Trial & Freemium Analysis
*   **Freemium:** Rejected. Freemium attracts low-quality leads, drains support resources, and dilutes the premium brand positioning.
*   **Free Trial:** 14-Day reverse-trial. Users get full access to the Pro tier. On day 14, they must enter a credit card to maintain Pro, or downgrade to a severely restricted (branded) Basic tier.

### 2.2. Tiered Plans (Standardized to USD for baseline)
*   **Basic Plan ($29/mo):** 1 Location, 100 items max, "Powered by SmartMenu" watermark, standard templates, no POS integration. Target: Food trucks, small cafes.
*   **Pro Plan ($79/mo):** 1-3 Locations, unlimited items, custom branding (no watermark), advanced analytics, priority support. Target: Standard restaurants.
*   **Enterprise Plan (Custom / Starting $299/mo):** Unlimited locations, centralized brand management, POS sync, SLA guarantees, dedicated account manager. Target: Chains, Hotel groups.

### 2.3. Billed Annually (Yearly Plans)
*   Offer a 20% discount for upfront annual payment (e.g., Pro at $758/year).
*   **Goal:** Rapidly acquire working capital to fund customer acquisition cost (CAC) payback within month 1.

### 2.4. B2B Specialized Plans
*   **White-Label Plan ($499/mo + $10/tenant):** Fully rebranded. The "SmartMenu" name never appears. Target: Large restaurant holding groups.
*   **Agency Plan ($199/mo base):** Allows marketing agencies to manage 10+ clients from a single master dashboard. Bulk discount pricing on sub-accounts.
*   **Franchise Plan:** Hub-and-spoke model. Head office pays base fee, forces menu changes down to franchisees. Franchisees pay a small local connection fee ($19/mo).

## 3. Growth Programs
### 3.1. Partner & Affiliate Program
*   **Structure:** 20% recurring commission on the lifetime of the referred account.
*   **Target:** Restaurant consultants, POS installers, marketing agencies.
*   **Payout:** Processed monthly via Stripe Connect.

### 3.2. Referral Program (Restaurant-to-Restaurant)
*   "Give $50, Get $50" applied as subscription credits.

### 3.3. Coupons & Gift Cards
*   Time-boxed promotional codes used strictly by Sales to close deals (e.g., `FIRSTMONTH50`). Never displayed publicly to prevent brand devaluation.

## 4. Lifecycle Flows
### 4.1. Renewal Flow
*   Auto-renews via Stripe.
*   Annual plans receive a mandatory email 15 days prior to billing (reducing chargeback disputes).

### 4.2. Upgrade Flow
*   Self-serve via Dashboard. Pro-rated instantly. Feature gates unlock immediately.

### 4.3. Downgrade Flow
*   Self-serve, but introduces friction. User must acknowledge lost features (e.g., "You will lose your custom branding and your menus will show ads. Are you sure?").

### 4.4. Cancellation Flow
*   Requires completing a mandatory 3-question survey.
*   Triggers an automated, immediate "Hail Mary" offer (e.g., "Stay for 3 months at 50% off").
*   If accepted, churn is prevented. If rejected, account is marked for soft-deletion.

### 4.5. Win-Back Flow
*   Day 30 post-cancellation: Automated email highlighting new features.
*   Day 90: High-discount offer via email.
*   Day 180: Manual SMS from Sales offering a free concierge setup if they return.

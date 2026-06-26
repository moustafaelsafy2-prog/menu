# UX ARCHITECTURE: DASHBOARD SCREENS (PART 2)

## SCREEN: Branch Manager
*   **Purpose:** Manage physical locations and assign menus.
*   **Target User:** Owner, Ops Manager.
*   **Entry Points:** Sidebar 'Branches'.
*   **Layout:** Card Grid (Desktop & Mobile).
*   **Cards:** Branch Card (Name, Status Badge, Address, Assigned Menu Dropdown, 'Manage QR' link).
*   **Primary CTA:** `+ Add Branch` (Opens Dialog).
*   **Secondary CTA:** `Edit Branch`.
*   **Empty State:** "You have no branches. Create one to generate a QR code."

## SCREEN: QR Manager
*   **Purpose:** Generate, download, and design the physical QR codes.
*   **Target User:** Ops Manager, Marketing.
*   **Entry Points:** Branch Card -> 'Manage QR'.
*   **Layout:** 2-Column (Controls on Left, Live Preview on Right).
*   **Forms:** Table Number input (optional), QR Style Selector (Colors, Logo embedding).
*   **Primary CTA:** `Download SVG` / `Download PNG`.
*   **Secondary CTA:** `Print All Table QRs` (Generates a batch PDF).
*   **Animation Notes:** QR preview re-renders instantly on color change.

## SCREEN: Users & Roles
*   **Purpose:** RBAC (Role-Based Access Control) management.
*   **Target User:** Owner.
*   **Entry Points:** Sidebar 'Users'.
*   **Layout:** Tabs (`Users` | `Roles`).
*   **Tables (Users Tab):** Name, Email, Role, Last Login, Status.
*   **Primary CTA:** `Invite User` (Opens Modal -> Enter Email, Select Role).
*   **Secondary CTA (Roles Tab):** `Create Custom Role` (Opens Drawer with permission checkboxes).
*   **Danger Actions:** `Revoke Access` (Requires Confirmation Dialog).

## SCREEN: Subscription & Billing
*   **Purpose:** SaaS management for the Tenant.
*   **Target User:** Owner only.
*   **Entry Points:** Sidebar 'Billing' or Top-bar 'Upgrade' badge.
*   **Sections:**
    1.  **Current Plan Card:** Shows 'Pro', renewal date, and usage limits.
    2.  **Payment Method:** Shows masked card ending in 4242.
    3.  **Invoice History:** Data table of past invoices.
*   **Primary CTA:** `Upgrade Plan` (Redirects to Stripe Checkout).
*   **Secondary CTA:** `Update Payment Method`.
*   **Danger Actions:** `Cancel Subscription`.
*   **Confirmation Dialogs:** "Are you sure you want to cancel? Your menus will go offline at the end of the billing period."

## SCREEN: Audit Logs
*   **Purpose:** Security and operational compliance tracking.
*   **Target User:** Owner.
*   **Entry Points:** Sidebar 'Audit Logs'.
*   **Layout:** Dense Data Table.
*   **Tables:** Timestamp, User, Action (e.g., "Deleted Category"), Entity.
*   **Filters:** Date Range, User Dropdown, Action Type.
*   **Actions:** `View Details` (Opens modal showing JSON diff of old/new values).
*   **Export:** `Export to CSV`.
*   **Performance Notes:** Infinite scrolling/cursor pagination required due to high volume.

## SCREEN: Settings (Tenant)
*   **Purpose:** Global workspace configurations.
*   **Target User:** Owner.
*   **Layout:** Vertical list of form sections.
*   **Sections:**
    1.  **General:** Restaurant Name, Timezone.
    2.  **Financial:** Currency, Default Tax Rate.
    3.  **Localization:** Default Language, Enabled Languages.
*   **Forms:** Standard inputs.
*   **Autosave:** No. Explicit `Save Changes` button required to prevent accidental financial changes.
*   **Validation:** Tax rate must be a valid percentage.

## SCREEN: Future Ordering Settings
*   **Purpose:** Reserved for future transactional capabilities.
*   **Target User:** Owner.
*   **Sections:** Order Types (Dine-in, Pickup), Tax Settings, Service Charges.
*   **State:** Currently sits behind a "Coming Soon" or "Upgrade to Unlock" paywall/feature-flag.

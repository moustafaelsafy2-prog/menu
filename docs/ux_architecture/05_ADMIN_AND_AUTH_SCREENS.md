# UX ARCHITECTURE: SUPER ADMIN & AUTH SCREENS

## SCREEN: Super Admin Dashboard
*   **Purpose:** The central command center for SmartMenu employees.
*   **Target User:** Platform Administrators, Support Staff.
*   **Entry Points:** Hidden URL (`/admin/login`).
*   **Layout:** Similar to B2B Dashboard, but strictly utilitarian (no brand themes).
*   **Sections:**
    1.  **Global Metrics:** Total Active Tenants, Monthly Recurring Revenue (MRR), Churn Rate, System Uptime.
    2.  **Recent Signups:** Table of the newest tenants.
    3.  **Active Incidents:** Alerts from Sentry/Datadog.
*   **Analytics Events:** Strictly tracked internal tooling metrics.

## SCREEN: Admin - Tenants List
*   **Purpose:** Managing the customer base.
*   **Target User:** Support Staff.
*   **Layout:** Dense Data Table.
*   **Tables:** Tenant Name, Owner Email, Plan, Status (Active/Suspended), Created Date.
*   **Actions (Hover):** `View Details`, `Impersonate`.
*   **Danger Actions:** `Suspend Tenant` (Requires a reason input in the confirmation dialog).
*   **Secondary CTA:** `Export to CSV`.

## SCREEN: Admin - Tenant Details
*   **Purpose:** Deep dive into a specific customer's account for support.
*   **Layout:** Multi-card grid.
*   **Cards:** Contact Info, Billing History, Usage Stats (Menu Scans, Storage used).
*   **Primary CTA:** `Impersonate Tenant` (Opens a new tab logged in as that user, strictly logged in the Audit Trail).

---

## AUTHENTICATION FLOWS

## SCREEN: Login
*   **Purpose:** Secure entry to the B2B Dashboard.
*   **Target User:** All Staff.
*   **Layout:** Centered Card on a split-screen (Left: Form, Right: Marketing Image/Testimonial).
*   **Forms:** Email Input, Password Input.
*   **Primary CTA:** `Log In`.
*   **Secondary CTA:** `Forgot Password?`.
*   **Validation:** Inline email format validation.
*   **Error State:** "Invalid credentials" toast. Does not specify *which* is wrong (security).
*   **Loading State:** Button shows spinner, inputs are disabled.

## SCREEN: Register (Tenant Onboarding)
*   **Purpose:** Creating a new account and workspace.
*   **Layout:** Stepper / Wizard.
*   **Sections:**
    1.  Step 1: Account (Name, Email, Password).
    2.  Step 2: Business (Restaurant Name, Country).
    3.  Step 3: Verification (Check email for OTP).
*   **Primary CTA:** `Next Step` / `Create Account`.
*   **Validation:** Password strength meter (Red/Yellow/Green).

## SCREEN: Forgot Password / Reset Password
*   **Purpose:** Account recovery.
*   **Layout:** Centered Card.
*   **Forms (Forgot):** Email Input.
*   **Primary CTA (Forgot):** `Send Reset Link`.
*   **Forms (Reset):** New Password, Confirm Password.
*   **Success State:** "Password reset successfully. Redirecting to login..."

## SCREEN: 2FA Setup / Verification (Future)
*   **Purpose:** Enhanced security for Owner accounts.
*   **Layout:** Centered Card.
*   **Forms:** 6-digit OTP Input (Auto-advances to next input box).
*   **Primary CTA:** `Verify`.
*   **Secondary CTA:** `Resend Code` (Disabled for 60 seconds, shows countdown).
*   **Accessibility:** OTP inputs must support pasting a 6-digit code.

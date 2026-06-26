# UI SPECIFICATION: AUTHENTICATION & ADMIN SCREENS

## 1. Authentication Screens (Login, Register)
*   **Screen Purpose:** Secure entry point to the B2B Dashboard.
*   **Desktop Layout:** 50/50 Split Screen.
    *   **Left Half (Form):** `bg-white flex items-center justify-center min-h-screen p-8`.
    *   **Right Half (Marketing):** `bg-neutral-900 hidden lg:flex flex-col justify-between p-12 text-white overflow-hidden relative`. Contains a subtle gradient mesh background and a large typographic testimonial.
*   **Mobile Layout:** 100% Form view. Marketing half is completely hidden. `bg-white px-4 py-12`.

### 1A. Form Container (Left Half)
*   **Grid:** Single column, max-width constrained. `w-full max-w-[400px] flex flex-col gap-6`.
*   **Header:**
    *   Brand Logo (Top, `mb-8 h-8`).
    *   Title: `text-3xl font-bold text-neutral-900 mb-2`.
    *   Subtitle: `text-base text-neutral-500`.

### 1B. Form Inputs
*   **Spacing:** Fields separated by `gap-4`.
*   **Label:** `block text-sm font-medium text-neutral-700 mb-1.5`.
*   **Input Field:**
    *   Height: `h-11` (44px).
    *   Padding: `px-3`.
    *   Visuals: `bg-white border border-neutral-300 rounded-md shadow-sm`.
    *   Typography: `text-base text-neutral-900 placeholder:text-neutral-400`.
    *   **State (Focus):** `outline-none ring-2 ring-brand-500/20 border-brand-500 transition-shadow duration-150`.
    *   **State (Error):** `ring-2 ring-danger-base/20 border-danger-base`.

### 1C. Form Actions
*   **Primary Button:** `w-full h-11 bg-neutral-900 text-white rounded-md mt-2 font-medium`. (Note: We use strict black/white for auth buttons to keep it brand-agnostic before tenant login).
*   **State (Loading):** Text opacity 0. Absolute positioned spinner center.
*   **Secondary Links:** `flex justify-between items-center mt-4 text-sm`. "Forgot Password?" links right.

---

## 2. Super Admin Platform
*   **Screen Purpose:** Internal management of the entire SaaS platform.
*   **Visual Language:** Strictly utilitarian. No consumer branding. Follows the Dashboard Design Language (Document 03) but uses `Neutral 900` (Black) for primary buttons instead of a specific Brand color.
*   **App Shell:**
    *   Sidebar is darker to differentiate from the Tenant Dashboard. `bg-neutral-900 text-neutral-300`.
    *   Active Sidebar Links: `bg-neutral-800 text-white`.

### 2A. Tenant Detail View (Admin Specific)
*   **Layout:** Complex 3-column masonry grid or structured multi-card layout `max-w-7xl mx-auto px-8 py-8`.
*   **Header Section:**
    *   Title: Tenant Name + Status Badge (Green "Active" or Red "Suspended").
    *   Top Right Actions: `Impersonate` (Outline button), `Suspend Account` (Danger button).
*   **Card 1: Contact Information:**
    *   Layout: Grid `grid-cols-2 gap-4`.
    *   Fields: Owner Name, Email, Phone, Created Date. Formatted as key/value pairs (`text-xs text-neutral-500 uppercase` above `text-sm font-medium text-neutral-900`).
*   **Card 2: Subscription & Billing Status:**
    *   Visuals: `bg-neutral-50 border border-neutral-200 p-5 rounded-lg`.
    *   Content: Current Plan, MRR, Stripe Customer ID.
*   **Card 3: Danger Zone (Bottom of page):**
    *   Visuals: `border border-danger-base rounded-lg p-5`.
    *   Content: "Delete Tenant Data". Button is `bg-danger-base text-white`. Requires typing the tenant's exact name into an input field to enable the button.

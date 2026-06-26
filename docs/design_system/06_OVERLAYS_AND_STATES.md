# SMARTMENU DESIGN SYSTEM: OVERLAYS & STATES

## 53-55. Dialogs, Bottom Sheets, Drawers
*   **Purpose:** Interruptive/Contextual actions without leaving the page.
*   **Variants:**
    *   **Dialog (Modal):** Centered on desktop. Used for confirmations (e.g., "Delete Category?").
    *   **Bottom Sheet:** Glued to bottom on mobile. Replaces dialogs on mobile viewports. Used for Product Details.
    *   **Drawer:** Slides from right. Used for complex forms (e.g., "Edit Product").
*   **States:** Opening, Open, Closing.
*   **Behavior:** Must capture a focus trap. Clicking the backdrop closes the overlay (unless destructive).
*   **Accessibility:** `role="dialog"`, `aria-modal="true"`. Escape key closes. Initial focus lands on the first interactive element or the primary action.
*   **Animations:**
    *   Dialog: Scale up from 0.95 and fade in.
    *   Bottom Sheet: Slide up from `translate-y-full`.
    *   Backdrop: Fade to `rgba(0,0,0,0.4)` with backdrop-blur.
*   **Do:** Use Bottom Sheets exclusively on mobile for better ergonomics.
*   **Don't:** Open a modal on top of a modal.

## 56-58. Snackbars, Toasts, Notifications
*   **Purpose:** Ephemeral system feedback.
*   **Variants:** Success (Green), Error (Red), Info (Neutral).
*   **Sizes:** Max-width 320px.
*   **Spacing:** Placed 24px from Bottom-Center (Mobile) or Bottom-Right (Desktop).
*   **Behavior:** Auto-dismisses after 4000ms. Pauses countdown on hover.
*   **Accessibility:** `role="alert"` or `aria-live="polite"`.
*   **Animations:** Slide up from bottom. Exit by fading out and sliding down.
*   **Do:** Keep text under 50 characters.
*   **Don't:** Put interactive buttons inside a toast (except for "Undo").

## 59. Loading States & 60. Skeleton Screens
*   **Purpose:** Managing perceived performance during async operations.
*   **Variants:**
    *   **Button Spinner:** Preserves button width, blocks double-clicks.
    *   **Page Loader:** Central spinner (Avoid if possible).
    *   **Skeleton Screen:** Mimics the layout of the incoming data.
*   **Behavior:** Skeletons are used for initial page loads (e.g., waiting for the menu to hydrate).
*   **Animations:** Skeletons use a `shimmer` gradient animation moving left-to-right (`duration-1500`).
*   **Do:** Match the skeleton shapes exactly to the expected content (circles for avatars, rectangles for text).
*   **Don't:** Show a skeleton for less than 300ms (causes a flashing effect). Delay the skeleton render by 200ms.

## 61. Empty States
*   **Purpose:** Guiding the user when no data exists.
*   **Variants:** Initial Empty (New account), Filter Empty (No search results).
*   **Spacing:** Centered in container, generous padding (`py-24`).
*   **Content:** Illustration (Neutral line-art), Title, Description, Primary Action Button (e.g., "Create your first category").
*   **Do:** Use empty states as onboarding opportunities.

## 62. Error States
*   **Purpose:** Handling failures gracefully.
*   **Variants:** Inline Form Error, 404 Page, 500 App Crash, Partial Failure (e.g., Image failed to load).
*   **Behavior:** Must provide a clear recovery path (e.g., "Try Again" button).
*   **Do:** Blame the system, not the user.
*   **Don't:** Show raw technical stack traces to the user.

## 63. Offline States
*   **Purpose:** Progressive Web App (PWA) resilience.
*   **Behavior:**
    *   **Menu:** If cached, show a subtle yellow banner: "You are viewing an offline version. Prices may not be current."
    *   **Dashboard:** Disable all `POST/PUT/DELETE` buttons. Show persistent offline banner.
*   **Animations:** Slide down from the top header.

# SMARTMENU DESIGN SYSTEM: MOTION, ERGONOMICS, & ACCESSIBILITY

## 64-66, 91-94. Motion, Transitions & Timing
*   **Philosophy:** Animation exists to explain state changes, not to entertain. If an animation delays a user, it is a failure.
*   **Motion Timing (Durations):**
    *   `fast`: 150ms (Hover states, color changes, toggles).
    *   `base`: 200ms (Dropdowns, modals scaling in).
    *   `slow`: 300ms (Bottom sheets sliding up, page transitions).
*   **Animation Curves (Easing):**
    *   `ease-out`: `cubic-bezier(0.0, 0.0, 0.2, 1)` (Elements entering the screen). Decelerates quickly.
    *   `ease-in`: `cubic-bezier(0.4, 0.0, 1, 1)` (Elements leaving the screen). Accelerates quickly.
    *   `ease-in-out`: `cubic-bezier(0.4, 0.0, 0.2, 1)` (Elements moving across the screen).
*   **Page Transitions:** Hard cuts on B2B dashboard. Subtle fade-in on Consumer Menu (`150ms ease-out`).
*   **Micro-interactions:** Haptic feedback integration via Web API (`navigator.vibrate`) on mobile when adding items to cart or toggling critical switches.

## 67. Gesture Rules
*   **Swipe Down:** Dismisses bottom sheets and full-screen image galleries.
*   **Swipe Left/Right:** Navigates between horizontal tabs or image carousels.
*   **Pinch:** Zooms into food photography on the product detail view.
*   **Rule:** Every gesture MUST have a visible button fallback (e.g., an 'X' button to close a sheet, even if swipe-down works).

## 68. Scrolling Rules
*   **Momentum Scrolling:** `webkit-overflow-scrolling: touch` is mandatory on all scrollable containers on iOS.
*   **Scroll Snapping:** Used strictly for horizontal image galleries (snapping to the start of the next image).
*   **Overscroll:** Block pull-to-refresh (`overscroll-behavior: none`) on the B2B dashboard to prevent accidental reloads while scrolling tables.

## 69-70. Accessibility & Contrast Rules
*   **Standard:** WCAG 2.1 Level AA strictly enforced.
*   **Contrast:**
    *   Normal text (under 18pt) must have a contrast ratio of at least 4.5:1 against its background.
    *   Large text (18pt+) and UI components (icons, borders) must have 3.0:1.
*   **Color Reliance:** Color cannot be the *only* visual means of conveying information. An error state must turn red AND show an error icon or explicit text.

## 71-73. Keyboard, Screen Reader, & Focus Rules
*   **Keyboard Navigation:** The entire B2B dashboard must be fully operable via Tab, Space, Enter, and Arrow keys.
*   **Focus Ring:** `outline: 2px solid var(--brand-500); outline-offset: 2px;`. Never use `outline: none` unless replacing it with a custom, highly visible focus ring.
*   **Focus Trap:** When a modal opens, focus is trapped inside it until closed.
*   **Screen Readers:**
    *   Use `aria-hidden="true"` on decorative icons.
    *   Use `sr-only` utility classes to visually hide labels that screen readers need (e.g., a search input with only a magnifying glass icon visually).
    *   Dynamic updates (e.g., "Item added to cart") must be announced via `aria-live="polite"`.

## 74. Touch Targets & 75-77. Viewport Ergonomics
*   **Touch Targets (Mobile):** The absolute minimum touch target size is 44x44px. This includes padding. (e.g., a 24px icon must have 10px padding on all sides).
*   **Mobile Rules (<768px):** Actions move to the bottom of the screen (thumb zone). Complex tables convert to stacked list cards. Hover states are ignored.
*   **Tablet Rules (768-1024px):** Dual-pane layouts introduced (e.g., List on left, details on right).
*   **Desktop Rules (>1024px):** Max-width constraints enforced to prevent infinite horizontal stretching. Hover states drive discovery.

# SMARTMENU DESIGN SYSTEM: COLOR & ELEVATION

## 14. Color Philosophy
Colors in the SmartMenu system are used functionally, not decoratively. The core UI is strictly monochromatic (black, white, and grays). Color is reserved *only* to indicate state (success, error), to highlight primary actions, and to represent the Tenant's brand identity.

## 15. Primary Palette (Tenant Brand)
This palette is dynamically injected based on the Tenant's Theme configuration.
*   **Brand 50:** Super light tint (Backgrounds for active states).
*   **Brand 100-400:** Transitional tints.
*   **Brand 500 (Base):** The core brand color. Used for primary buttons, active tabs, and primary links.
*   **Brand 600:** Used for hover states on primary buttons.
*   **Brand 900:** Darkest shade.

## 16. Secondary Palette
Reserved strictly for marketing or complex illustrations. Never used in the core operational UI.

## 17. Neutral Palette (The Core UI)
Our neutrals are "warm" to evoke a hospitality feel, rather than cold "tech" blue-grays.
*   **Neutral 0:** `#FFFFFF` (Card backgrounds)
*   **Neutral 50:** `#FAFAFA` (App background light mode)
*   **Neutral 100:** `#F4F4F5` (Hover states on list items)
*   **Neutral 200:** `#E4E4E7` (Borders, dividers)
*   **Neutral 300:** `#D4D4D8` (Disabled states)
*   **Neutral 400:** `#A1A1AA` (Placeholder text, muted icons)
*   **Neutral 500:** `#71717A` (Secondary text, metadata)
*   **Neutral 600:** `#52525B` (Strong secondary text)
*   **Neutral 800:** `#27272A` (Primary text, headings)
*   **Neutral 900:** `#18181B` (App background dark mode)

## 18. Semantic Palette (Success, Warning, Danger, Info)
Used strictly for status indicators and destructive actions.
*   **Success (Green):**
    *   Base: `#10B981` (Used for "Paid", "Completed", Checkmarks)
    *   Bg: `#ECFDF5`
*   **Warning (Yellow/Orange):**
    *   Base: `#F59E0B` (Used for "Pending", Low Stock alerts)
    *   Bg: `#FFFBEB`
*   **Danger (Red):**
    *   Base: `#EF4444` (Used for "Failed", "Delete", "Sold Out")
    *   Bg: `#FEF2F2`
*   **Info (Blue):**
    *   Base: `#3B82F6` (Used for tooltips, new feature announcements)
    *   Bg: `#EFF6FF`

## 19. Dark Mode
Dark mode is not an inversion; it is a recalculation of depth.
*   Backgrounds shift to `Neutral 900`.
*   Cards sit at `Neutral 800`.
*   Primary text shifts to `Neutral 50` (never pure `#FFFFFF` to reduce eye strain).
*   Shadows are removed; depth is communicated via border strokes (`Neutral 700`) and slight background lightening.

## 20. Light Mode
The default state. Utilizes pure white cards on an off-white `Neutral 50` background to establish subtle hierarchy.

## 21. Elevation System
We communicate hierarchy through Z-index elevation.
*   **Elevation 0:** Backgrounds (`Neutral 50`).
*   **Elevation 1:** Cards, Buttons (Resting state).
*   **Elevation 2:** Floating Headers, Sticky Navigations.
*   **Elevation 3:** Dropdowns, Popovers.
*   **Elevation 4:** Modals, Dialogs.
*   **Elevation 5:** Toasts, Snackbars (Absolute highest).

## 22. Shadow System
Shadows are extremely soft, expansive, and use a tint of the neutral palette, never pure black.
*   **Shadow-Sm:** `0 1px 2px 0 rgba(0, 0, 0, 0.05)` (Buttons, subtle cards)
*   **Shadow-Md:** `0 4px 6px -1px rgba(0, 0, 0, 0.1)` (Hovered cards, dropdowns)
*   **Shadow-Lg:** `0 10px 15px -3px rgba(0, 0, 0, 0.1)` (Modals, bottom sheets)
*   **Shadow-Xl:** `0 20px 25px -5px rgba(0, 0, 0, 0.1)` (Reserved for marketing hero elements)

## 23. Border Radius System
*   **Radius-Sm (4px):** Inputs, small buttons, checkboxes. (Utilitarian)
*   **Radius-Md (8px):** Standard cards, menus.
*   **Radius-Lg (16px):** Featured product cards, bottom sheets.
*   **Radius-Full (9999px):** Pills, tags, circular avatars.

## 24. Stroke System
*   **Width 1px:** The standard border for cards, inputs, and dividers. Uses `Neutral 200`.
*   **Width 2px:** Used to indicate focus rings for accessibility (Uses `Brand 500` or `Info Base`).
*   **Width 0px:** Elements elevated with shadows rarely use strokes in Light Mode, but *must* use a 1px stroke in Dark Mode to define edges.

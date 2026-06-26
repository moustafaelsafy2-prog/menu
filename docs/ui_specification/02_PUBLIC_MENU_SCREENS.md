# UI SPECIFICATION: PUBLIC MENU SCREENS

## 1. Menu Home (The Core Catalog)
*   **Screen Purpose:** Browsing the restaurant catalog. Optimized for mobile, scaled for desktop.
*   **Visual Hierarchy:** 1. Hero Image / Brand Logo. 2. Category Navigation (Sticky). 3. Product Cards.
*   **Grid (Desktop):** Centered container `max-w-3xl` (768px). Mimics a mobile layout on desktop to maintain reading rhythm.
*   **Grid (Mobile):** 100% width, `px-4` (16px) margins for text, images bleed to edge.

### 1A. Header & Navigation (Sticky)
*   **Exact Position:** `fixed top-0 w-full z-20`.
*   **Padding:** `py-3 px-4`.
*   **Visuals:** `bg-neutral-0/80` (80% opacity white) with `backdrop-blur-md` (12px). Border-bottom `1px solid Neutral 200`.
*   **Layout:** Flexbox, `justify-between`, `items-center`.
*   **Elements:**
    *   Left: Search Icon (`Icon-Md`).
    *   Center: Brand Text (`text-lg font-bold`) or Logo (`max-h-8`).
    *   Right: Language Switcher (Text 'AR' or 'EN', `text-sm font-medium`).

### 1B. Category Navigation (ScrollSpy Pill Nav)
*   **Exact Position:** `sticky top-[56px] z-10` (Sits right below the header).
*   **Padding:** `py-3 px-4`.
*   **Layout:** Horizontal scroll container (`overflow-x-auto whitespace-nowrap`). Hides scrollbar (`::-webkit-scrollbar { display: none }`).
*   **Pill Component:**
    *   Padding: `px-4 py-1.5`.
    *   Border Radius: `rounded-full`.
    *   Text: `text-sm font-medium`.
    *   **State (Active):** `bg-brand-500 text-white`.
    *   **State (Inactive):** `bg-neutral-100 text-neutral-600 border border-neutral-200`.

### 1C. Product Card (List View Variant)
*   **Spacing:** `mb-4` between cards.
*   **Layout:** Flexbox row. `gap-4`. Padding `p-4`.
*   **Visuals:** `bg-white rounded-lg shadow-sm border border-neutral-100`.
*   **Image (Left):** `w-[80px] h-[80px] rounded-md object-cover flex-shrink-0`.
*   **Content (Right):** `flex-col flex-1 justify-between`.
    *   Title: `text-base font-semibold text-neutral-800 line-clamp-2`.
    *   Description: `text-sm text-neutral-500 line-clamp-2 mt-1`.
    *   Price Row: `flex justify-between items-center mt-2`.
        *   Price: `text-base font-bold text-neutral-900`.
        *   Calories: `text-xs text-neutral-400`.
*   **State (Hover - Desktop only):** `shadow-md translate-y-[-1px] transition-all duration-200`.
*   **State (Pressed - Mobile):** `scale-95 bg-neutral-50 duration-100`.
*   **State (Sold Out):** Image filter `grayscale(100%) opacity(50%)`. Title text color `text-neutral-400`. Price `line-through`. Red badge `absolute top-2 right-2 px-2 py-1 bg-danger-base text-white text-xs font-bold rounded`.

---

## 2. Product Details (Bottom Sheet)
*   **Screen Purpose:** Expanding an item to read full description and view larger gallery.
*   **Exact Layout:** Bottom sheet rooted to the bottom of the viewport.
*   **Responsive Behavior:**
    *   Mobile: Sheet covers bottom 85% of screen (`h-[85vh]`). Border radius top-left/right 16px.
    *   Desktop: Renders as a centered Modal (`max-w-md`, rounded 16px all sides) instead of a bottom sheet.

### 2A. Sheet Header
*   **Position:** Sticky top inside the sheet. `z-10 bg-white/90 backdrop-blur`.
*   **Layout:** Flex, `justify-end`, `p-4`.
*   **Elements:** Circular close button (`w-8 h-8 rounded-full bg-neutral-100 flex items-center justify-center`).

### 2B. Image Gallery
*   **Position:** Below header. Bleeds to left/right edges on mobile.
*   **Sizing:** Aspect ratio `4:3` or `1:1`. `w-full object-cover`.
*   **Pagination:** Horizontal scroll snapping (`snap-x snap-mandatory`). Pagination dots centered below image (`w-1.5 h-1.5 rounded-full`, active is `bg-neutral-800`, inactive is `bg-neutral-300`).

### 2C. Content Area
*   **Padding:** `p-5`.
*   **Hierarchy:**
    1.  Header Row: Title (`text-2xl font-bold`) left, Price (`text-xl font-bold text-brand-500`) right.
    2.  Badges Row: `flex gap-2 mt-3`. Tag format (`px-2 py-1 rounded bg-neutral-100 text-xs font-medium`).
    3.  Description: `text-base text-neutral-600 leading-relaxed mt-4`.
*   **Scroll Behavior:** The content area scrolls vertically while the sheet itself remains fixed at 85vh.

---

## 3. Search Fullscreen Overlay
*   **Screen Purpose:** Rapid item discovery.
*   **Layout:** Takes over entire viewport `fixed inset-0 z-50 bg-white`.
*   **Animation:** Fades in `opacity-0` to `opacity-100` over `150ms`.

### 3A. Search Header
*   **Layout:** Flex row, `p-4 border-b border-neutral-200`.
*   **Elements:**
    *   Search Input: `flex-1 bg-neutral-100 rounded-md h-10 px-3 flex items-center gap-2`.
    *   Input Text: `text-base text-neutral-900`. No outline on focus.
    *   Cancel Button: `text-sm font-medium text-neutral-600 ml-4` (Right aligned).

### 3B. Results List
*   **Layout:** `flex-col px-4 pt-4 pb-safe`.
*   **Empty State:** Shown immediately before typing. "Search for dishes, ingredients..."
*   **No Results State:** "No exact matches. Try another term." centered, `mt-12`.
*   **Results Cards:** Identical to Menu Home List View, but without the card border (uses simple bottom-border dividers `border-b border-neutral-100 pb-4 mb-4`) to increase density.

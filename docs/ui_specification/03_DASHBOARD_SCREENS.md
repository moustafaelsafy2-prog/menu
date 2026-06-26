# UI SPECIFICATION: B2B DASHBOARD SCREENS

## 1. Global Dashboard Layout (App Shell)
*   **Screen Purpose:** The bounding container for all authenticated B2B routes.
*   **Desktop Layout (>1024px):**
    *   **Sidebar:** Fixed left, `w-[240px]`, `h-screen`, `bg-white border-r border-neutral-200`.
    *   **Main Content:** `ml-[240px] flex-1 bg-neutral-50 min-h-screen`.
*   **Tablet Layout (768px - 1024px):**
    *   **Sidebar:** Collapsed to icon-only, `w-[64px]`. Hover expands to 240px.
    *   **Main Content:** `ml-[64px]`.
*   **Mobile Layout (<768px):**
    *   **Sidebar:** Hidden. Replaced by a Top Navbar (`h-[56px]`) with a Hamburger Menu icon.
    *   **Drawer:** Hamburger opens sidebar as a left-side drawer overlay (`z-50`).

### 1A. Sidebar Component
*   **Padding:** `py-6 px-4`.
*   **Header:** Brand Logo (`h-8 mb-8`).
*   **Nav List:** `flex-col gap-1`.
*   **Nav Item:**
    *   Layout: `flex items-center gap-3 px-3 py-2 rounded-md`.
    *   Typography: `text-sm font-medium`.
    *   **State (Default):** `text-neutral-600 hover:bg-neutral-100`.
    *   **State (Active):** `bg-brand-50 text-brand-500`.

### 1B. Topbar Component (Content Area Header)
*   **Position:** Sticky top inside Main Content area. `h-[64px] bg-white/80 backdrop-blur z-20 border-b border-neutral-200`.
*   **Padding:** `px-8`.
*   **Layout:** Flex, `justify-between items-center`.
*   **Left:** Page Title (`text-xl font-bold text-neutral-900`) or Breadcrumbs (`text-sm text-neutral-500`).
*   **Right:** Flex gap-4. Notifications Icon, User Avatar (Circular `w-8 h-8 rounded-full bg-neutral-200`).

---

## 2. Analytics Dashboard View
*   **Grid Configuration:** 12-column sub-grid applied to a `max-w-7xl mx-auto px-8 py-8` container.

### 2A. Statistic Cards (KPI Widgets)
*   **Layout:** `col-span-12 md:col-span-6 lg:col-span-3`. (4 cards across on desktop, 2 on tablet, 1 on mobile).
*   **Visuals:** `bg-white rounded-lg border border-neutral-200 p-5 shadow-sm`.
*   **Content:**
    *   Label: `text-sm font-medium text-neutral-500 mb-1`.
    *   Value: `text-3xl font-bold text-neutral-900`.
    *   Delta Indicator: Flex row below value. Icon (Up/Down arrow) + `text-sm font-medium`. `text-success-base` if positive, `text-danger-base` if negative.

### 2B. Main Chart Widget
*   **Layout:** `col-span-12 lg:col-span-8`.
*   **Visuals:** Same card base as KPIs. Height: `h-[400px]`.
*   **Header:** Title left, Date Range Select right (`h-8 text-sm`).
*   **Chart UI:** Line chart. Line color `Brand 500`. Fill below line uses vertical gradient (`Brand 500` at 20% opacity to 0% at bottom). No horizontal grid lines, only subtle vertical ticks.

---

## 3. Data Table View (Products, Users)
*   **Container:** `bg-white rounded-lg border border-neutral-200 overflow-hidden shadow-sm m-8`.

### 3A. Table Toolbar
*   **Padding:** `p-4 border-b border-neutral-200`.
*   **Layout:** Flex, `justify-between items-center`.
*   **Left (Search):** Input component `w-64`. Left icon `MagnifyingGlass`.
*   **Right (Actions):** Flex `gap-2`.
    *   Filter Button (`Secondary` variant, icon left).
    *   Create Button (`Primary` variant, `Brand 500`, icon left).

### 3B. Table Structure
*   **Element:** Semantic `<table>` width `100%`.
*   **Header Row (`<thead>`):**
    *   `bg-neutral-50 border-b border-neutral-200 text-left`.
    *   Cells (`<th>`): `px-4 py-3 text-xs font-semibold text-neutral-500 uppercase tracking-wider`.
*   **Body Rows (`<tbody>`):**
    *   Cells (`<td>`): `px-4 py-4 whitespace-nowrap text-sm text-neutral-800 border-b border-neutral-100`.
    *   **State (Hover):** Row background turns `bg-neutral-50` for easy tracking across columns.
*   **Column Alignment:**
    *   Text/Names: Left aligned.
    *   Prices/Numbers: Right aligned (tabular lining figures).
    *   Actions/Toggles: Right aligned.

### 3C. Table Inline Actions (Context Menu)
*   **Element:** 'Three dots' icon button on the far right of a row.
*   **Interaction:** Click opens a Dropdown menu (`absolute z-30 bg-white border rounded shadow-lg`).
*   **Dropdown Items:** Edit (Neutral), Duplicate (Neutral), Delete (Danger, `text-danger-base`).

### 3D. Table Pagination Footer
*   **Padding:** `px-4 py-3 bg-white border-t border-neutral-200`.
*   **Layout:** Flex, `justify-between items-center`.
*   **Content:** Text "Showing 1 to 10 of 97 results".
*   **Buttons:** "Previous" and "Next" (`Secondary` variant, disabled if at start/end).

# UX ARCHITECTURE: DASHBOARD SCREENS (PART 1)

## SCREEN: Dashboard Home (Analytics Overview)
*   **Purpose:** High-level operational pulse of the restaurant.
*   **Target User:** Restaurant Owner, General Manager.
*   **Entry Points:** Login Success, Sidebar 'Home' link.
*   **Exit Points:** Deep links to specific reports or catalog management.
*   **Layout:** 12-column Grid (Desktop), Single Column (Mobile).
*   **Sections:**
    1.  **Welcome Banner:** "Good evening, Tariq." + Quick Setup Actions if incomplete.
    2.  **Top KPIs (Cards):** Total Scans, Active Menus, Top Branch.
    3.  **Live Traffic Chart:** Line chart of scans today vs yesterday.
    4.  **Top Products Table:** Quick list of most viewed items.
*   **Cards:** Metric Card (Title, Value, Delta % Indicator).
*   **Charts:** Area Chart (Time series).
*   **Filters:** Date Range Picker (Top right, defaults to 'Today').
*   **Primary CTA:** "View Full Analytics"
*   **Secondary CTA:** "Edit Menu"
*   **Loading State:** Card-shaped Skeletons.
*   **Empty State:** "No data yet. Print your QR codes to get started."
*   **Desktop Layout:** Sidebar Left, Content Right. KPI cards 4-up.
*   **Mobile Layout:** KPI cards 2-up or 1-up.
*   **Analytics Events:** `admin_view(dashboard)`.

## SCREEN: Products List (Catalog)
*   **Purpose:** Manage the core inventory of dishes.
*   **Target User:** Manager, Data Entry Staff.
*   **Entry Points:** Sidebar 'Products'.
*   **Exit Points:** Create Product (Drawer), Edit Product (Drawer).
*   **Layout:** Standard Data Table view.
*   **Sections:** 1. Page Header (Title + CTA), 2. Toolbar (Search, Filter, Bulk Actions), 3. Data Table.
*   **Tables:** Products Table (Image Thumbnail, Name, Category, Price, Status Toggle).
*   **Forms:** Inline search input.
*   **Filters:** Filter by Category, Filter by Status (Available/Hidden).
*   **Primary CTA:** `+ New Product` (Opens Right Drawer).
*   **Secondary CTA:** `Export CSV`.
*   **Danger Actions:** `Delete Selected` (Appears only when rows are checked).
*   **Confirmation Dialogs:** "Are you sure you want to delete 3 products? This cannot be undone."
*   **Loading State:** Table skeleton rows.
*   **Empty State:** "You have no products. [Add your first product]"
*   **Desktop Layout:** Full-width table.
*   **Mobile Layout:** Converts table to a vertical list of cards.

## SCREEN: Create/Edit Product (Drawer)
*   **Purpose:** Data entry for a single dish.
*   **Target User:** Manager.
*   **Entry Points:** `+ New Product` or clicking a row in Products List.
*   **Exit Points:** Cancel button, Save button.
*   **Layout:** Right-side Drawer overlay (Desktop), Full-screen (Mobile).
*   **Forms:**
    *   Image Upload Zone (Drag & drop).
    *   Basic Info (Name [AR/EN], Category Dropdown, Price).
    *   Metadata (Calories, Dietary Tag Checkboxes).
    *   Status (Available Toggle).
*   **Validation:** Price must be >= 0. Name cannot be empty.
*   **Primary CTA:** `Save Product` (Sticky bottom).
*   **Secondary CTA:** `Cancel`.
*   **Autosave:** No autosave on creation. Yes for drafts in future.
*   **Unsaved Changes:** Dialog warns: "You have unsaved changes. Discard?"
*   **Animation Notes:** Drawer slides in from right `300ms ease-out`. Backdrop fades in.

## SCREEN: Categories Manager
*   **Purpose:** Manage menu taxonomy and ordering.
*   **Target User:** Manager.
*   **Entry Points:** Sidebar 'Categories'.
*   **Layout:** Draggable List / Tree view.
*   **Sections:** 1. Header, 2. Draggable List.
*   **Actions:** Drag-handle to reorder.
*   **Primary CTA:** `+ New Category`.
*   **Animation Notes:** React Beautiful DnD fluid layout transitions when dropping an item.

## SCREEN: Media Library
*   **Purpose:** Central repository for all uploaded images.
*   **Target User:** Marketing Manager, Owner.
*   **Entry Points:** Sidebar 'Media', or spawned from an Image Upload zone.
*   **Layout:** Fluid Masonry Grid.
*   **Sections:** 1. Upload Dropzone, 2. Image Grid.
*   **Selection:** Click an image to select it (adds a blue border and checkmark).
*   **Bulk Actions:** Delete Selected.
*   **Loading State:** Blank gray squares with shimmer.

## SCREEN: Theme & Branding
*   **Purpose:** Customizing the look of the public menu.
*   **Target User:** Owner, Designer.
*   **Entry Points:** Sidebar 'Branding'.
*   **Layout:** Split Screen. Left: Controls. Right: Live Mobile Preview.
*   **Forms:** Color Picker (Primary Color), Font Selector (Dropdown), Layout Selector (Radio buttons: Grid vs List).
*   **Primary CTA:** `Publish Theme`.
*   **Animation Notes:** Changes on the left instantly update the iframe preview on the right (React state sync).

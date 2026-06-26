# UX ARCHITECTURE: PUBLIC CUSTOMER SCREENS

## SCREEN: QR Landing (Routing)
*   **Purpose:** Invisible router that takes a short-hash (sm.to/XYZ) and redirects to the correct branch menu.
*   **Target User:** Diner scanning physical QR.
*   **Entry Points:** Native phone camera, generic QR scanner.
*   **Exit Points:** Redirect to `/menus/{tenant_slug}?branch={branch_id}`.
*   **Layout:** Centered loading layout (Mobile only).
*   **Sections:** 1. Centered Spinner, 2. Brand Logo (if cached).
*   **Loading State:** Subtle pulse animation on logo.
*   **Performance Notes:** MUST resolve in < 150ms. Renders at Edge.

## SCREEN: Menu Home (The Core Catalog)
*   **Purpose:** The primary browsing experience. Displays all categories and products.
*   **Target User:** Diner at the table.
*   **Entry Points:** QR Landing, Direct URL.
*   **Exit Points:** Product Details (Bottom Sheet), Search (Overlay), Language Toggle.
*   **Layout:** Mobile-first, single continuous vertical scroll.
*   **Sections:**
    1.  **Header:** Brand Logo (Center), Lang Toggle (Right), Search Icon (Left).
    2.  **Hero/Offers:** Horizontal scrolling carousel of featured items.
    3.  **Category Navigation:** Sticky horizontal pill menu (Spy-scrolls with page).
    4.  **Category Sections:** Vertical list of categories, containing product cards.
    5.  **Footer:** Contact info, "Powered by SmartMenu".
*   **Cards:** Product Card (Image, Name, Price, Calories, Badges).
*   **Dialogs:** None. (Uses Bottom Sheets).
*   **Navigation:** Category Pill Nav (Horizontal scroll).
*   **Primary CTA:** Tap Product Card (Opens Details).
*   **Secondary CTA:** Call Waiter (Floating Action Button - FAB).
*   **Loading State:** Skeleton blocks matching the exact geometry of the product grid.
*   **Empty State:** "Menu is currently unavailable."
*   **Offline State:** Banner: "Offline Mode. Prices may be outdated."
*   **Mobile Layout:** 1-column list or 2-column tight grid.
*   **Desktop Layout:** Max-width 768px centered container (Mimics mobile on desktop).
*   **Accessibility Notes:** Category nav must support horizontal swipe and keyboard tabbing.
*   **Animation Notes:** Header height shrinks on scroll-down. Category pills slide horizontally.
*   **Analytics Events:** `page_view(menu_home)`, `category_view(id)`.
*   **Action (Click):** Open Product Detail.
*   **Action (Swipe):** Navigate horizontal carousels.

## SCREEN: Product Details (Bottom Sheet)
*   **Purpose:** Deep dive into a specific dish.
*   **Target User:** Diner evaluating an item.
*   **Entry Points:** Tap on Product Card in Menu Home.
*   **Exit Points:** Swipe down, Tap 'X', Tap outside sheet.
*   **Layout:** Bottom Sheet (Occupies 85% of screen height on mobile).
*   **Sections:**
    1.  **Header:** Sticky Top. Contains 'X' close button and share icon.
    2.  **Hero Image:** 1:1 or 4:3 aspect ratio, edge-to-edge inside sheet.
    3.  **Title & Price:** Large typography, prominent.
    4.  **Badges:** Dietary, Spice level.
    5.  **Description:** Multi-line text.
    6.  **Modifiers (Future-proof):** "Add Extra Cheese" (Disabled UI for V1 view-only).
*   **Primary CTA:** N/A for V1 (View only). Future: "Add to Cart" sticky at bottom.
*   **Secondary CTA:** Share Item.
*   **Loading State:** Image Blurhash placeholder.
*   **Animation Notes:** Slides up from bottom `300ms ease-out`. Image slightly zooms out on load.
*   **Analytics Events:** `product_view(id)`.
*   **Action (Swipe Down):** Close sheet (Velocity sensitive).

## SCREEN: Search Overlay
*   **Purpose:** Finding specific items instantly.
*   **Target User:** Diner who knows exactly what they want (e.g., "Latte").
*   **Entry Points:** Search Icon in Header.
*   **Exit Points:** 'Cancel' button, Tap outside.
*   **Layout:** Full-screen overlay.
*   **Sections:** 1. Search Input (Auto-focused), 2. Recent Searches (if applicable), 3. Results List.
*   **Forms:** Search Input (`type="search"`).
*   **Validation:** Debounce input by 300ms before querying.
*   **Loading State:** Inline spinner inside the search input.
*   **Empty State:** "No results found for 'X'. Try searching for 'Burger'."
*   **Mobile Layout:** Keyboard pushes results up.
*   **Action (Keyboard):** Submits search, dismisses keyboard.

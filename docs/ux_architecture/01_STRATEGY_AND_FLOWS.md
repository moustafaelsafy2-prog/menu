# UX ARCHITECTURE: STRATEGY & FLOWS

## 1. User Flow Diagrams (Text Representation)
*   **The "Hungry Diner" Flow:**
    `Scan QR on Table` -> `QR Landing (Resolves Branch)` -> `Splash (Preloads Images)` -> `Menu Home (Category List)` -> `Scroll to 'Burgers'` -> `Tap 'Truffle Burger'` -> `Product Details Bottom Sheet` -> `Swipe down to close` -> `Tap 'Call Waiter'` -> `Confirmation Toast`.
*   **The "Friday Rush 86" Flow (Staff):**
    `Open Dashboard on Mobile` -> `Tap 'Products' icon in Bottom Nav` -> `Search 'Salmon'` -> `Toggle 'Available' Switch to OFF` -> `Snackbar: "Salmon marked Out of Stock"`.
*   **The "Onboarding" Flow (Owner):**
    `Landing Page` -> `Register (Email/Pass)` -> `Tenant Setup Wizard (Name, Logo, Colors)` -> `Dashboard Home (Empty State)` -> `Tap 'Create Category'` -> `Form Entry` -> `Success`.

## 2. Task Flow Diagrams
*   **Task: Add a new Modifier Group (e.g., "Meat Temperature")**
    1. Navigate to `Modifiers` screen.
    2. Click Primary CTA `+ New Group`.
    3. Drawer opens from right.
    4. Focus on `Internal Name` input. Enter "Meat Temp".
    5. Set `Min Selections` = 1, `Max Selections` = 1 (Creates Radio behavior).
    6. Click `+ Add Option`. Enter "Rare", Price "0.00".
    7. Click `Save` (Primary CTA in Drawer).
    8. Drawer closes, `Modifiers` table updates, Success Toast appears.

## 3. Navigation Flow (Information Architecture)
*   **Consumer PWA (Mobile):**
    *   Sticky Top: Header (Brand Logo, Language Toggle, Search Icon).
    *   Scrolling Content: Hero Image, Category Pills (Sticky on scroll), Product Grid.
    *   Overlays: Product Details (Bottom Sheet), Search Fullscreen.
*   **B2B Dashboard (Desktop):**
    *   Left Sidebar: `Home`, `Analytics`, `Catalog` (Collapsible: `Products`, `Categories`, `Modifiers`), `Media`, `Design` (`Theme`, `QR`), `Settings`.
    *   Top Bar: Breadcrumbs, Global Search (Cmd+K), User Avatar (Profile/Logout Menu).
    *   Main Content Area: Varies per route.

## 4. Journey Maps
*   **Persona:** "Tariq", Restaurant Owner setting up for the first time.
    *   *Phase 1 (Discovery):* Expects easy setup. High anxiety about technical complexity.
    *   *Phase 2 (Setup):* Uses the Setup Wizard. Delight when the logo upload automatically suggests a primary color for the theme.
    *   *Phase 3 (Execution):* Bulk uploads items via CSV. Relief that he doesn't have to type 200 items.
    *   *Phase 4 (Deployment):* Generates PDF of QRs. Excitement. Prints and places on tables.
    *   *Phase 5 (Observation):* Checks Analytics after first dinner service. High satisfaction seeing 150 unique scans.

## 5. State Machine Logic (Global UI States)
*   **System Status:**
    *   `Idle`: Normal operation.
    *   `Mutating`: Data is being saved. All primary forms disable their Submit button, changing text to "Saving...".
    *   `Offline`: Browser reports `navigator.onLine == false`. A persistent yellow banner drops down. Mutative actions are disabled. Read actions serve stale cache.
    *   `Error`: Backend returns 5xx. A global Error Boundary catches the crash, replacing the main view with the `500 Server Error` screen.
    *   `Session Expired`: 401 Unauthorized received. User is immediately redirected to `/login?reason=expired`.

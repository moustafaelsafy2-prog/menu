# SMARTMENU DESIGN SYSTEM: NAVIGATION & DATA COMPONENTS

## 39-42. Navigation, Bottom Nav, Sidebar, Header
*   **Purpose:** Wayfinding throughout the application.
*   **Variants:**
    *   **Sidebar (Dashboard):** Fixed left 240px. Collapses to icons only on tablet.
    *   **Bottom Navigation (Diner PWA):** Fixed bottom. 3-5 destinations max.
    *   **Header:** Sticky top. Contains Breadcrumbs (Dashboard) or Restaurant Branding (Menu).
*   **States:** Active route, Inactive route, Hover.
*   **Behavior:** Sticky positioning. Header uses blur backdrop (`backdrop-filter: blur(12px)`) to allow scrolling content to show through faintly.
*   **Accessibility:** Use `<nav>` semantic tags. Current route marked with `aria-current="page"`.
*   **Animations:** Active state icon fills solid. Sidebar sliding drawer on mobile (`translate-x-full` to `0`).

## 43-44. Search Components & Filters
*   **Purpose:** Discovery of content.
*   **Variants:** Prominent Hero Search, Inline Table Search, Filter Drawer.
*   **Behavior:** Search auto-focuses on desktop (Cmd+K). Filters update URL query params instantly.
*   **Accessibility:** `role="search"`. Clear button must be keyboard focusable.
*   **Animations:** Filter chips scale in when added.

## 45. Badges & 46. Tags
*   **Purpose:** Metadata categorization (e.g., "Vegan", "Spicy", "New").
*   **Variants:** Solid (High emphasis), Subdued (Low emphasis, tinted background), Outline.
*   **States:** Static, Removable (contains 'x' icon).
*   **Sizes:** `text-xs` (Height 20px) or `text-sm` (Height 24px).
*   **Spacing:** `px-2 py-0.5`.
*   **Behavior:** Usually non-interactive. Removable variants act as filter clearers.
*   **Do:** Use Semantic colors (Green = Vegan, Red = Spicy).
*   **Don't:** Overuse badges. A product card should max have 3 tags to avoid clutter.

## 47. Product Cards
*   **Purpose:** Core entity display for the diner menu.
*   **Variants:**
    *   **List View:** Image left (aspect-square, 80px), Text/Price right.
    *   **Grid View:** Image top (aspect-square, full width), Text/Price below.
    *   **Hero View:** Edge-to-edge image, gradient scrim, floating text (used for featured items).
*   **States:** Available, Sold Out (Greyscale image, strike-through price), Loading (Skeleton).
*   **Spacing:** 16px gap between image and text.
*   **Behavior:** Clicking opens the Product Detail Bottom Sheet.
*   **Accessibility:** Entire card is a touch target. Image requires `alt` text.
*   **Animations:** Image subtly scales `1.05` on hover.

## 48. Category Cards
*   **Purpose:** Top-level menu navigation.
*   **Variants:** Text Pill (Horizontal scroll), Image Tile (Grid).
*   **Behavior:** Active category pill highlights and sticks to top on scroll (ScrollSpy).

## 49. Restaurant Cards
*   **Purpose:** Used in multi-branch views or superadmin lists.
*   **Variants:** Horizontal list item.
*   **Data Displayed:** Logo, Name, Location/Distance, Open/Closed Badge.

## 50-51. Analytics Cards & Charts
*   **Purpose:** Business intelligence visualization.
*   **Variants:** Big Number Card, Line Chart (Revenue over time), Bar Chart (Top selling items), Heatmap (Busy hours).
*   **Behavior:** Tooltips on hover/touch.
*   **Animations:** Bars grow vertically from 0. Lines draw from left to right on load.
*   **Do:** Keep charts strictly monochromatic (Brand color) unless comparing disparate data series.
*   **Don't:** Use 3D charts or pie charts with > 5 slices.

## 52. Tables
*   **Purpose:** Dense B2B data management.
*   **Variants:** Standard, Compact, Selectable (Bulk Actions).
*   **Sizes:** Row height 56px (Standard) or 40px (Compact).
*   **Spacing:** Cell padding 16px.
*   **Behavior:** Fixed header on scroll. Horizontal scroll on small viewports.
*   **Accessibility:** Proper use of `<th>`, `scope`, and `aria-sort`.
*   **Animations:** Hover state changes row background to `Neutral 50`.
*   **Do:** Right-align numerical data (Prices). Left-align text.
*   **Don't:** Wrap text infinitely. Use truncation with a tooltip on hover.

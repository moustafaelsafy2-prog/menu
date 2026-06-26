# ENGINEERING BIBLE: FRONTEND ENGINEERING

## 1. Component Architecture (Next.js App Router)
SmartMenu relies heavily on the Next.js App Router to achieve Edge-level performance.
*   **React Server Components (RSC):** Default for everything. Data fetching, layouts, and static UI must be RSCs. They ship zero JavaScript to the client.
*   **Client Components:** Used *strictly* for interactivity (e.g., `onClick`, `useState`, `useEffect`). Marked with `"use client"`. Pushed as far down the component tree as possible (Leaves, not Branches).

## 2. Feature Folders (Colocation)
Code is organized by feature, not by type.
*   **Bad:** `/components/ProductCard.tsx`, `/hooks/useProduct.ts`, `/types/Product.ts`.
*   **Good:** `/features/products/ProductCard.tsx`, `/features/products/useProduct.ts`, `/features/products/types.ts`.

## 3. State Management
*   **Server State:** Handled natively by Next.js `fetch` cache or React Query (if polling is required).
*   **URL State:** The URL is the single source of truth for UI state (search, filters, pagination). Use `nuqs` (Next Use Query State) or native `searchParams`.
*   **Local State:** `useState` / `useReducer`.
*   **Global Client State:** Zustand. Redux is strictly forbidden due to boilerplate overhead. Zustand stores must be atomic and sliced.

## 4. Data Fetching & Caching
*   **Rule:** Fetch data where it is consumed. RSCs eliminate the need for global data stores.
*   **Public Menu:** Fetched at the Edge with `stale-while-revalidate`.
*   **Dashboard:** Fetched dynamically `cache: 'no-store'`.
*   **Mutations:** Use Server Actions for all form submissions. Call `revalidatePath` or `revalidateTag` immediately after mutation to bust the Router Cache.

## 5. Error & Loading Boundaries
*   Every distinct layout segment must have a `loading.tsx` (using the Design System Skeleton) and an `error.tsx` (using the Design System Error State).
*   Errors must be caught gracefully; a failure in the Analytics widget must not crash the rest of the Dashboard page.

## 6. Suspense Strategy
Wrap distinct data-fetching components in `<Suspense fallback={<Skeleton />}>`. This allows the page shell to render instantly while slow data (like external POS sync status) streams in.

## 7. Code Splitting & Lazy Loading
*   Heavy third-party libraries (e.g., Chart.js, PDF generation) MUST be dynamically imported using Next.js `dynamic()`.
*   Modals and Drawers should be dynamically imported so their code isn't loaded until the user clicks to open them.

## 8. Image Optimization
*   `next/image` is mandatory. Bare `<img>` tags are forbidden.
*   `sizes` attribute must be perfectly calibrated to viewport breakpoints to prevent downloading a 4K image on a mobile device.
*   `placeholder="blur"` using Base64 Blurhashes is mandatory for all food photography.

## 9. SEO Strategy
*   Applies to the Public Menu app only.
*   `metadata` exports in `layout.tsx` and `page.tsx` must be dynamic (e.g., resolving the restaurant's name and SEO description).
*   Semantic HTML (`<main>`, `<article>`, `<nav>`) is required.

## 10. Accessibility Implementation
*   ESLint `eslint-plugin-jsx-a11y` is enforced in strict mode. CI fails if an `alt` tag is missing.
*   Radix UI primitives are used for complex interactive components (Dialogs, Selects, Tabs) to guarantee WAI-ARIA compliance, focus trapping, and keyboard navigation.

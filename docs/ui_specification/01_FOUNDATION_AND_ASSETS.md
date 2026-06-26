# UI SPECIFICATION: FOUNDATION & VISUAL ASSETS

## 1. Iconography Implementation Spec
*   **Library:** Phosphor Icons (Line style).
*   **Sizing constraints:**
    *   `Icon-Sm`: 16x16px container (Used in inline buttons, table cells).
    *   `Icon-Md`: 20x20px container (Used in standard buttons, navigation links).
    *   `Icon-Lg`: 24x24px container (Used in main navigation headers, empty states).
*   **Alignment:** Vertically centered with adjacent text using `align-items: center` and `gap: 8px` (`space-2`).
*   **Color mapping:** Inherits `currentColor`.
    *   Active Nav: `text-brand-500` (Fills solid).
    *   Inactive Nav: `text-neutral-500`.
    *   Actionable: `text-neutral-800`.
    *   Disabled: `text-neutral-300`.
*   **Micro-interaction:** Hovering a standalone icon button scales it to `1.1` with `duration-150 ease-out`.

## 2. Illustrations & Photography Usage
*   **Empty State Illustrations:**
    *   **Position:** Centered in container (`justify-center items-center`).
    *   **Sizing:** Max-width 160px.
    *   **Spacing:** `margin-bottom: 24px` (`space-6`) before the title.
    *   **Color:** Monochromatic stroke (`Neutral 300`) with a single dot of `Brand 500`.
*   **Food Photography (Menu UI):**
    *   **Corner Radius:**
        *   Hero/Header: `0px` (Edge-to-edge).
        *   Product Details: `0px` top, `0px` bottom.
        *   Grid Product Card: `border-radius-md` (8px) on top edges only.
        *   List Product Card: `border-radius-md` (8px) on all edges.
    *   **Protection:** Hero images must overlay a CSS linear gradient scrim (`rgba(0,0,0,0)` to `rgba(0,0,0,0.6)`) ending 30% from the bottom to protect text contrast.

## 3. Empty States & Error Illustrations UI
*   **Empty State Layout:**
    *   Container: `display: flex`, `flex-direction: column`, `align-items: center`.
    *   Padding: `py-24` (96px).
    *   Hierarchy:
        1.  Illustration (160px width, `mb-6`).
        2.  Title (`text-xl`, `font-semibold`, `text-neutral-800`, `mb-2`).
        3.  Description (`text-base`, `text-neutral-500`, `text-center`, `max-w-sm`, `mb-6`).
        4.  Primary Button (Standard `Md` size, `Brand 500`).
*   **Error State Layout (404/500):**
    *   Same structural layout as Empty States.
    *   Title changes to `text-danger-base`. Button text is "Reload Page" or "Go Home".

## 4. Loading Skeletons UI
*   **Animation:** Standard shimmer. Background `Neutral 100`, shimmer gradient `Neutral 200`.
*   **List Item Skeleton:**
    *   Container: `flex gap-4 p-4 border-b border-neutral-200`.
    *   Image Block: `80x80px`, `rounded-md`.
    *   Text Block: `flex-col gap-2`.
        *   Title line: `h-5 w-3/4 rounded`.
        *   Desc line 1: `h-4 w-full rounded`.
        *   Desc line 2: `h-4 w-5/6 rounded`.
        *   Price line: `h-5 w-1/4 rounded mt-2`.

## 5. Micro-Interactions UI Spec
*   **Button Press:** Active state applies `transform: scale(0.97)` and slightly darkens the background (`Brand 600`). Returns to `1.0` via a spring animation curve (`type: spring, stiffness: 400, damping: 25`).
*   **Switch Toggle:** Thumb translates `translateX(16px)`. Track background transitions from `Neutral 300` to `Success Base` over `150ms`.
*   **Toast Entrance:** Slides in from `translateY(100%)` to `translateY(0)` over `300ms ease-out`.

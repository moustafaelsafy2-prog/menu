# SMARTMENU DESIGN SYSTEM: VISUAL & MEDIA

## 25. Icon System
*   **Library:** Phosphor Icons or Heroicons (Outline variant).
*   **Weight:** Strictly 1.5px stroke width. Never solid/filled unless denoting an "active" state (e.g., Active Home Tab vs Inactive Home Tab).
*   **Size Base:** 24x24px bounding box.
*   **Corner Radius:** Rounded caps and joins to soften the interface.
*   **Color:** Inherits current text color (`currentColor`), usually `Neutral 500` or `Neutral 800`.
*   **RTL:** Directional icons (arrows, chevrons) MUST flip in RTL. Absolute concepts (clocks, checkmarks) DO NOT flip.

## 26. Illustration Style
*   **Philosophy:** Illustrations are used sparingly, primarily for Empty States.
*   **Style:** Line-art, single continuous stroke, monochromatic (`Neutral 300`) with a single accent dot or splash using the `Brand 500` color.
*   **Tone:** Playful but sophisticated (e.g., a simple line drawing of a cloche for an empty menu, rather than a cartoon chef).

## 27. Photography Style
Photography is the focal point of SmartMenu.
*   **UI Interference:** The UI must never overlay solid text on top of photography without a strict gradient scrim (black to transparent, 60% opacity) to guarantee WCAG contrast ratios.
*   **Loading:** All images must utilize a Base64 Blurhash placeholder while the main image loads to prevent layout shifting and white flashes.

## 28. Food Photography Rules (Guidelines for Tenants)
These rules are presented to the restaurant owner during onboarding:
*   **Lighting:** Natural or soft-box lighting. No harsh flash (creates unappetizing oily highlights).
*   **Background:** Neutral, uncluttered surfaces (marble, wood, solid colors).
*   **Composition:** Shot from 45-degree angle or top-down (flat lay).
*   **Format:** Images must be uploaded without text or logos embedded in the image itself.
*   **Cropping:** The subject (the food) should occupy 70% of the frame.

## 29. Image Ratios
To maintain a rigid, predictable grid, we enforce strict aspect ratios via CSS `aspect-ratio` regardless of the uploaded image size.

*   **1:1 (Square):** Used for Grid Layout product cards, Category thumbnails, and Modifier icons.
*   **4:3 (Landscape):** Used for List Layout product thumbnails and standard gallery images.
*   **16:9 (Widescreen):** Used for Hero Headers at the top of the menu, or featured promotional banners.
*   **Behavior:** Images must always use `object-fit: cover` to fill the ratio without distortion.

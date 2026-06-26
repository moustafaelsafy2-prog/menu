# SMARTMENU DESIGN SYSTEM: PHILOSOPHY & FOUNDATION

## 1. Design Philosophy
The SmartMenu Design System (codenamed "Aura") is built on the premise that hospitality software should feel invisible. It exists to elevate the food, not to draw attention to itself. We reject bloated, colorful interfaces in favor of quiet, structural elegance. The interface should feel as tactile and premium as a leather-bound menu in a Michelin-starred restaurant, yet as fast and fluid as native iOS.

## 2. Brand Personality
*   **Minimalist, not sterile:** We use abundant whitespace, but warm typography and subtle textures prevent coldness.
*   **Authoritative, not arrogant:** We guide the user confidently (clear primary actions) without overwhelming them.
*   **Tactile, not flat:** We use subtle depth, blur, and motion to emulate physical interaction.
*   **Cultural, not generic:** The system respects Arabic typography as a primary citizen, not an afterthought.

## 3. Design Principles
1.  **Content is King; UI is the Frame:** The food photography is the only thing allowed to be visually loud. The UI remains strictly monochromatic and structurally quiet.
2.  **Zero Cognitive Load:** Diners are hungry; they should not have to learn an interface. Interactions must be instantly predictable (Linear/Stripe style).
3.  **Ruthless Speed:** An animation must never block a user action. Transitions must feel instantaneous.
4.  **Absolute Accessibility:** Beauty means nothing if it cannot be used. We adhere to strict WCAG 2.1 AA standards for contrast and screen readers.
5.  **Flawless RTL:** The right-to-left experience must feel natively designed, not mechanically flipped.

## 4. Visual Language
The visual language relies on strict geometric proportions, deeply saturated neutral tones (off-blacks, warm grays), and perfectly sharp edges contrasted with deliberate, large-radius curves on interactive elements to invite touch.

## 5. Grid System
We use a fluid, 8-point based grid system.
*   **Mobile (<768px):** 4 columns. Margins: 16px. Gutters: 16px.
*   **Tablet (768px - 1024px):** 8 columns. Margins: 32px. Gutters: 24px.
*   **Desktop (>1024px):** 12 columns. Margins: Auto (Max-width 1440px). Gutters: 24px.
*   **B2B Dashboard Specific:** Fluid width with a fixed 240px left sidebar. The content area uses a 12-column sub-grid.

## 6. Spacing System
All spacing (padding, margin, gaps) MUST be a multiple of 4px (The 4pt Grid). No arbitrary values.
*   `space-1`: 4px (Micro-adjustments)
*   `space-2`: 8px (Between icon and text)
*   `space-3`: 12px (Inside small components)
*   `space-4`: 16px (Standard padding, component gaps)
*   `space-6`: 24px (Section padding on mobile)
*   `space-8`: 32px (Between major layout blocks)
*   `space-12`: 48px (Between distinct conceptual sections)
*   `space-16`: 64px (Page margins on desktop)
*   `space-24`: 96px (Hero section padding)

## 7. Layout Rules
*   **Rhythm:** Vertical rhythm is maintained by strictly adhering to the spacing scale.
*   **Containment:** Content is grouped using whitespace first, subtle borders second, and distinct background colors last.
*   **Alignment:** Text is always left-aligned (or right-aligned in RTL). Never justified. Center alignment is strictly reserved for empty states or hero headers.

## 8. Responsive Rules
*   **Mobile First:** The consumer menu is designed for a 375x812px viewport first. Desktop is an expansion.
*   **Fluid Typography:** Font sizes scale smoothly between breakpoints using `clamp()` rather than hard media queries.
*   **Touch Priority:** On viewports < 1024px, all interactive elements must assume touch (min 44x44px target).

## 9. Typography System
Typography is the most critical element of the Aura system. We use two distinct typeface categories to separate the "Utility" (Dashboard) from the "Experience" (Consumer Menu).

## 10. Font Pairing
*   **System (B2B Dashboard):** `Inter` (LTR) / `IBM Plex Sans Arabic` (RTL). Highly legible, neutral, data-dense.
*   **Display (Consumer Menu):** A high-contrast, elegant serif for headings (e.g., `Playfair Display` or `Noto Serif Arabic`) paired with a clean geometric sans-serif for body copy (e.g., `Satoshi` or `Tajawal`). *Note: Tenants can override the Consumer Menu fonts via the Theme engine.*

## 11. Font Scale
Based on a 1.250 (Major Third) modular scale. Base size is 16px.
*   `text-xs`: 12px / Line Height: 16px / Letter-spacing: 0.02em (Labels, metadata)
*   `text-sm`: 14px / Line Height: 20px / Letter-spacing: 0 (Secondary text)
*   `text-base`: 16px / Line Height: 24px / Letter-spacing: -0.01em (Body copy)
*   `text-lg`: 18px / Line Height: 28px / Letter-spacing: -0.015em (Lead paragraphs)
*   `text-xl`: 20px / Line Height: 28px / Letter-spacing: -0.02em (Card Titles)
*   `text-2xl`: 24px / Line Height: 32px / Letter-spacing: -0.02em (Section Headings)
*   `text-3xl`: 30px / Line Height: 36px / Letter-spacing: -0.02em (Page Headings)
*   `text-4xl`: 36px / Line Height: 40px / Letter-spacing: -0.02em (Hero Headings)

## 12. RTL Rules (Arabic)
*   **Structural Mirroring:** Everything flips (padding, margins, flex directions) except icons that have an inherent directional meaning that shouldn't change (e.g., a clock icon moving clockwise).
*   **Typography Adjustments:** Arabic typefaces often sit differently on the baseline and appear visually smaller. When `dir="rtl"`, base font sizes are bumped by +10% (e.g., 16px -> 18px) to match the visual weight of the English equivalent.
*   **Line Height:** Arabic requires +20% line height compared to Latin to accommodate deep descenders (e.g., 'ح', 'ع').

## 13. LTR Rules (English)
*   Standard left-to-right flow.
*   Tracking (letter-spacing) is tightened slightly on headings (-0.02em) for a more premium, editorial feel.

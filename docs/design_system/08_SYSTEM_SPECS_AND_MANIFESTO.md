# SMARTMENU DESIGN SYSTEM: SPECS & MANIFESTO

## 78-81. Domain Design Languages
We maintain three distinct visual dialects under the Aura Design System:
*   **Customer Menu Language:** Highly emotional, image-driven, expansive whitespace. Focuses on desire and ease of ordering. Uses Display typography. Hide complexity.
*   **Restaurant Dashboard Language:** Dense, utilitarian, data-heavy. Focuses on speed of execution for staff. Uses System typography. Minimizes scrolling.
*   **Superadmin Language:** Strictly functional. No brand colors. Raw data tables and charts. Built for platform engineers.

## 82-84. Hierarchy, Density, & Whitespace
*   **Visual Hierarchy:** Established primarily by typography scale and font weight, followed by color contrast, and finally spatial positioning.
*   **Content Density:**
    *   Dashboard: High density (Comfortable layout, minimal gaps).
    *   Menu: Low density (Luxurious whitespace, large imagery).
*   **Whitespace Strategy:** Whitespace is an active element, used as a structural divider instead of drawing physical lines (`hr` or borders) whenever possible.

## 85-87. Component Naming & Interaction Rules
*   **Naming:** Use strict atomic design naming in Figma/Code (`Atom/Button/Primary/Default`).
*   **Interaction Rule:** An interaction should never trigger a layout shift. If a button reveals a dropdown, it floats over content; it does not push content down.

## 88-90. Design Tokens, Tailwind, & Figma Mapping
The system is built on a shared language of Design Tokens.
*   **Figma:** Variables mapped to primitive tokens (e.g., `Color/Brand/500`).
*   **Tailwind:** `tailwind.config.js` maps perfectly to Figma tokens.
    *   `bg-brand-500`
    *   `text-neutral-800`
    *   `shadow-md`
    *   `rounded-lg`
*   No hardcoded hex values or pixel counts are permitted in the codebase or design files outside the token definitions.

## 95. Performance Budget
*   **CSS Size:** The compiled CSS bundle must remain under 15KB (gzipped). Tailwind's JIT compiler enforces this.
*   **Font Payload:** Custom web fonts must be subsetted (removing unused glyphs) and served in `WOFF2` format, remaining under 50KB total.
*   **LCP (Largest Contentful Paint):** The hero image of the menu must load in < 1.2s on a fast 3G connection.

## 96-98. Quality Assurance & Heuristics
*   **Accessibility Checklist:**
    1.  Can I tab through the entire page?
    2.  Are focus rings visible?
    3.  Do all images have `alt` text?
    4.  Does color contrast pass WebAIM tests?
*   **UX Heuristics (Nielsen):**
    1.  *Visibility of system status:* Loading states present?
    2.  *Error prevention:* Are destructive actions confirmed via dialog?
    3.  *Consistency:* Do primary buttons always sit on the right/bottom?

## 99. Future Component Library Strategy
*   **Framework Agnostic Design:** The CSS/Tokens are framework agnostic, though initially implemented in React.
*   **Headless UI:** We rely on Radix UI or Headless UI for the complex logic (focus trapping, keyboard navigation of dropdowns) and apply our Tailwind tokens strictly for the visual layer.

---

## 100. FINAL DESIGN MANIFESTO

**I. We Design for the Chaos of the Kitchen and the Quiet of the Table.**
Our software exists in two distinct worlds. In the back, it must withstand the panic of a Friday night dinner rush—meaning targets must be large, contrast high, and workflows instantly recognizable. In the front, it sits alongside crystal glasses and fine dining—meaning it must be elegant, silent, and beautiful.

**II. Speed is the Ultimate Luxury.**
No amount of visual polish can compensate for a slow interface. A diner will not wait 4 seconds for an animation to finish to see the price of a coffee. Performance is not an engineering metric; it is the core UX metric.

**III. Respect the Typography.**
The Middle East is our market. Arabic is a beautiful, complex script. It is never an afterthought, never mechanically flipped without care, and never squeezed into line-heights designed for English.

**IV. Form Follows Food.**
The UI is a picture frame. The food is the art. We use grayscale so the colors of the dish can breathe. We do not compete with the chef's work; we elevate it.

*This concludes the Aura Design System. No design decisions shall contradict these pillars.*

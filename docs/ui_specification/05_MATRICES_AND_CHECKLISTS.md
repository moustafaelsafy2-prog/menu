# UI SPECIFICATION: MATRICES & CHECKLISTS

## 1. Master Component Usage Matrix

| Component | Public Menu | Dashboard | Admin | Rules / Notes |
| :--- | :--- | :--- | :--- | :--- |
| **Primary Button** | Action (Call Waiter) | Save / Create | Impersonate | Never place 2 primary buttons side-by-side. |
| **Secondary Button**| Share, Cancel | Cancel, Filter | Export | Used to back out of actions or secondary tools. |
| **Data Table** | NEVER | Products, Users | Tenants List | Tables are strictly for B2B. Do not use on mobile viewports. |
| **Bottom Sheet** | Product Details | NEVER | NEVER | Bottom sheets are mobile-only UX. Dashboards use Drawers/Modals. |
| **Drawers** | NEVER | Edit Forms | Edit Settings | Slides from Right. Traps focus. |
| **Sticky Header** | Yes (Brand/Search) | Yes (Topbar) | Yes (Topbar) | Must use `backdrop-blur` to maintain scroll context. |
| **Toast/Snackbar** | Yes (Added to Cart)| Yes (Saved) | Yes (Actioned) | Bottom-center on mobile, Bottom-right on desktop. |

## 2. Responsive Matrix (Breakpoints & Behavior)

| Breakpoint | Public Menu Behavior | Dashboard Behavior |
| :--- | :--- | :--- |
| **< 768px (Mobile)** | Edge-to-edge. Bottom Sheets for details. | Sidebar hidden (Hamburger). Tables convert to stacked cards. |
| **768px - 1024px** | Centered `max-w-md` container. Modals replace sheets. | Sidebar collapsed to icons. Tables scroll horizontally. |
| **> 1024px (Desktop)**| Centered `max-w-3xl` container. | Sidebar expanded (240px). Fluid 12-column grid for content. |

## 3. Accessibility Matrix

| UI Element | WCAG Role | Aria Attributes Required | Keyboard Action |
| :--- | :--- | :--- | :--- |
| **Button** | `button` | `aria-disabled` (if disabled) | `Space` / `Enter` to trigger. |
| **Modal / Dialog**| `dialog` | `aria-modal="true"`, `aria-labelledby` | `Esc` to close. Focus trapped inside. |
| **Tabs** | `tablist`, `tab` | `aria-selected="true/false"` | `Left/Right Arrow` to switch. |
| **Toast Alert** | `alert` | `aria-live="polite"`, `role="status"` | N/A (Auto dismisses, but must not trap focus). |
| **Menu Switch** | `switch` | `aria-checked="true/false"` | `Space` to toggle. |

## 4. Animation Matrix

| Interaction | Element | CSS Transition / Keyframes | Duration / Easing |
| :--- | :--- | :--- | :--- |
| **Hover Button** | Background Color | `transition-colors` | `150ms ease-out` |
| **Press Button** | Scale | `transform: scale(0.97)` | `100ms ease-in` |
| **Open Modal** | Opacity & Scale | `@keyframes zoom-in { 0% {opacity:0, scale:0.95} }`| `200ms cubic-bezier(0.4, 0, 0.2, 1)` |
| **Open Drawer** | Translate X | `translate-x-full` to `translate-x-0` | `300ms cubic-bezier(0.4, 0, 0.2, 1)` |
| **Skeleton Load** | Background Pos | Linear background infinite slide | `1.5s linear infinite` |

## 5. Visual QA Checklist (For Frontend Engineers)
Before merging any UI code, the developer must verify:

**Layout & Spacing:**
- [ ] Is padding exactly matching the 4px grid? (e.g., 16px, 24px, no 15px or 22px).
- [ ] Do tables collapse properly on mobile screens or do they break the viewport?
- [ ] Does the UI layout correctly in RTL mode (Arabic text, right-aligned, mirrored icons)?

**Typography & Color:**
- [ ] Does text contrast pass WCAG AA (4.5:1)? Check gray text specifically.
- [ ] Are font sizes scaling using relative units (`rem`) to respect OS-level font settings?
- [ ] Is the primary Brand Color used ONLY for primary actions and active states?

**Interaction & States:**
- [ ] Do all buttons and links have distinct `:hover` and `:focus-visible` states?
- [ ] Is the focus ring visible when tabbing via keyboard?
- [ ] Does clicking the backdrop close the modal/drawer?
- [ ] Are `disabled` states visually distinct (opacity 50% or grayed out)?

**Performance & Media:**
- [ ] Do images load with a Blurhash or Skeleton placeholder before appearing?
- [ ] Is `object-fit: cover` applied to prevent image stretching?
- [ ] Are there zero layout shifts (CLS) when data hydrates into the DOM?

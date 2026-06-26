# SMARTMENU DESIGN SYSTEM: CORE COMPONENTS

## 30. Card System
*   **Purpose:** The fundamental container for grouping related information.
*   **Variants:** Elevated (Shadow), Outlined (Border 1px), Filled (Bg Neutral 50).
*   **States:** Resting, Hover (Elevated), Pressed (Scale down 0.98), Disabled (Opacity 50%).
*   **Sizes:** N/A (Fluid width).
*   **Spacing:** `p-4` (16px) or `p-6` (24px) internal padding.
*   **Behavior:** Acts as a click target if representing a product or link.
*   **Accessibility:** If clickable, must have `role="button"` or act as an `<a>` tag wrap.
*   **Animations:** `transition-shadow duration-200 ease-out` on hover.
*   **Do:** Use consistent padding.
*   **Don't:** Nest elevated cards within elevated cards.
*   **Examples:** Product Card, Dashboard Stat Widget.

## 31. Button System
*   **Purpose:** Triggers actions.
*   **Variants:** Primary (Solid Brand), Secondary (Outline Neutral), Tertiary (Ghost/Text), Destructive (Solid Danger).
*   **States:** Default, Hover, Active, Disabled, Loading.
*   **Sizes:**
    *   Sm: Height 32px, Text `text-sm`, px-3
    *   Md (Default): Height 40px, Text `text-sm`, px-4
    *   Lg: Height 48px, Text `text-base`, px-6
*   **Spacing:** Gap between icon and text is `space-2` (8px).
*   **Behavior:** Text centered. Icon optional (left or right).
*   **Accessibility:** Minimum touch target on mobile is 44px (Lg button required). Must have visible focus ring.
*   **Animations:** Background color crossfade `duration-150`. Scale to 0.97 on `:active`. Loading state replaces text with an infinite spinning loader, preserving button width.
*   **Do:** Use one Primary button per view.
*   **Don't:** Use multiple primary buttons adjacent to each other.

## 32. Input System (Text)
*   **Purpose:** Free-form text entry.
*   **Variants:** Default, Floating Label (Menu side), Inline (Dashboard table edits).
*   **States:** Default, Hover, Focus (Border Brand 500 + Ring), Error (Border Danger Base), Disabled (Bg Neutral 100).
*   **Sizes:** Height 40px (Dashboard), Height 48px (Consumer).
*   **Spacing:** `px-3` internal padding.
*   **Behavior:** Placeholder text disappears on input.
*   **Accessibility:** Must have an associated `<label>` (visually hidden if floating). `aria-invalid` true on error.
*   **Animations:** Focus ring transition `duration-150 ease-in-out`.
*   **Do:** Provide clear, inline error messages below the input.
*   **Don't:** Rely on placeholder text as a label (it disappears).

## 33. Dropdown System & 34. Select System
*   **Purpose:** Selecting one option from a list.
*   **Variants:** Native Select (Mobile), Custom Dropdown (Desktop).
*   **States:** Closed, Open, Disabled.
*   **Sizes:** Matches Input System.
*   **Spacing:** Options have `py-2 px-3`.
*   **Behavior:** Custom dropdowns must trap focus. Clicking outside closes the dropdown.
*   **Accessibility:** Keyboard navigation (Up/Down arrows to highlight, Enter to select, Escape to close).
*   **Animations:** Dropdown panel fades in and slides down slightly (`translate-y-1` to `0`).
*   **Do:** Use native `<select>` elements on mobile devices to leverage OS-level UI wheels.
*   **Don't:** Put a dropdown inside a scrolling modal if it causes clipping.

## 35. Checkbox & 36. Radio
*   **Purpose:** Boolean selections. Checkbox = Multi-select. Radio = Single-select.
*   **Variants:** Standard, Card-style (clicking the whole card toggles the input).
*   **States:** Unchecked, Checked, Indeterminate (Checkbox only), Disabled.
*   **Sizes:** Box is 16x16px or 20x20px.
*   **Spacing:** `space-2` between box and label.
*   **Behavior:** Toggling immediately updates local state.
*   **Accessibility:** Label must be clickable (linked via `id` and `for`).
*   **Animations:** Checkmark draws in `duration-200`. Fill color transitions.
*   **Do:** Stack vertically for easy scanning.
*   **Don't:** Use a checkbox when an immediate system state changes (use Switch instead).

## 37. Switch (Toggle)
*   **Purpose:** Immediate activation/deactivation of a setting (e.g., "Item Available").
*   **Variants:** Standard, Small (Table rows).
*   **States:** Off (Neutral 300), On (Success Base or Brand 500), Disabled.
*   **Sizes:** Track is 36x20px. Thumb is 16x16px.
*   **Spacing:** Inline with text label.
*   **Behavior:** Triggers a backend action immediately, rather than waiting for a "Save" button.
*   **Accessibility:** Must be semantic `<button role="switch" aria-checked="true/false">`.
*   **Animations:** Thumb translates X. Track color fades.
*   **Do:** Use for "Out of Stock" toggles.
*   **Don't:** Require a "Submit" button after toggling a switch.

## 38. Tabs
*   **Purpose:** Navigating between sibling views without leaving the context.
*   **Variants:** Underline (Dashboard), Segmented Control/Pill (Mobile Menu).
*   **States:** Default, Hover, Active (Selected).
*   **Sizes:** Height 40px.
*   **Spacing:** `px-4` between tabs.
*   **Behavior:** Scrollable horizontally on mobile if tabs exceed viewport width.
*   **Accessibility:** `role="tablist"`, `role="tab"`, `aria-selected`. Keyboard Left/Right arrows navigate.
*   **Animations:** Active indicator (underline or pill background) slides fluidly between selected items using Framer Motion/Layout animations.
*   **Do:** Keep tab names short (1-2 words).
*   **Don't:** Stack tabs into multiple rows. Make them horizontally scrollable instead.

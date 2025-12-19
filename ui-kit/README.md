# Escapemaster UI Kit - Design System & Component Library

## 1. Design Philosophy
The Escapemaster UI Kit is not just a collection of components; it is the manifestation of our design language. It is built on the principles of **Consistency**, **Accessibility**, and **Efficiency**.
- **Atomic Design**: We follow Brad Frost's Atomic Design methodology (Atoms -> Molecules -> Organisms).
- **Headless Logic**: Where possible, we separate behavior from presentation to allow for maximum flexibility.
- **Theme-First**: Every component is built from the ground up to support dynamic theming.

---

## 2. Design Tokens & Theming
We do not use hardcoded hex values in our components. Instead, we use a semantic abstraction layer.

### 2.1 Color Semantics
- `var(--primary)`: The main brand color. Used for primary actions, active states, and key highlights.
- `var(--secondary)`: Supporting color for accents and secondary actions.
- `var(--background)`: The canvas color. Changes significantly between Light/Dark modes.
- `var(--surface)`: The color for cards, modals, and elevated elements.
- `var(--text-main)`: High-contrast text for readability.
- `var(--text-muted)`: Lower contrast for metadata and placeholders.

### 2.2 Typography Scale
We use a fluid typography scale rooted in the **Inter** font family for maximum legibility on screens.
- `text-xs`: 0.75rem (12px) - Badges, tiny metadata.
- `text-sm`: 0.875rem (14px) - Standard body text, table data.
- `text-base`: 1rem (16px) - Primary inputs, headers.
- `text-lg` to `text-3xl`: Headings and display text.

---

## 3. Component Library Reference

### 3.1 Layout Primitives
- **`Card`**: The fundamental building block.
  - *Props*: `className`, `noPadding`, `hoverEffect`.
  - *Usage*: Used for dashboard widgets, lists, and forms.
- **`Container`**: Centers content with responsive max-widths.
- **`Grid`**: A wrapper around CSS Grid with predefined responsive column props.

### 3.2 Form Elements
- **`Button`**:
  - *Variants*: `primary`, `secondary`, `outline`, `ghost`, `danger`.
  - *Sizes*: `sm`, `md`, `lg`.
  - *Features*: Built-in loading spinner support, icon slots.
- **`Input`**:
  - *Features*: Integrated error message display, icon prefixes/suffixes.
- **`Select`**:
  - A custom implementation (not native select) allowing for rich content in dropdowns (icons, descriptions).

### 3.3 Data Display
- **`Badge`**:
  - *Variants*: `success` (Green), `warning` (Yellow), `error` (Red), `info` (Blue), `neutral` (Gray).
  - *Usage*: Status indicators for bookings and users.
- **`Avatar`**:
  - Displays user image or initials fallback. Supports status dot indicators.
- **`Table`**:
  - A composable table system (`Table`, `TableHeader`, `TableRow`, `TableCell`) allowing for complex layouts.

---

## 4. Implementation Details
The UI Kit is currently co-located within the repositories but is architected to be extracted into a private NPM package (`@escapemaster/ui`) in the future.
- **Tailwind Merge (`tw-merge`)**: We use this utility to intelligently merge custom classes with default component styles, resolving conflicts automatically.
- **Class Variance Authority (`cva`)**: Used to define component variants in a type-safe, declarative manner.

## 5. Accessibility (a11y) Standards
- **Keyboard Navigation**: All interactive elements are focusable and operable via keyboard.
- **ARIA Attributes**: Components include necessary ARIA labels and roles for screen readers.
- **Contrast Ratios**: All default color pairings meet WCAG AA standards.

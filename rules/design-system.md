# Design System — shadcn/ui Foundations

Component base for all platforms. Web and desktop shells (browser, Electron, Tauri) use shadcn/ui directly. React Native has no official shadcn/ui: use a shadcn-equivalent primitive set (e.g. a maintained port such as react-native-reusables) and keep the same token names, variants, and behavior so these rules transfer unchanged.

## Component policy

- Build from shadcn/ui primitives. Complex behaviors — dialogs, dropdown menus, popovers, sheets/drawers, command palettes, selects, tabs, toasts (Sonner) — are never hand-rolled.
- Interactive elements are real controls: `<button>`, `<a href>`, `<input>`, `<select>`. A clickable `<div>`/`<span>` is a rejection.
- Compose; do not fork. Extend components with props and variants (`cva`), not by copying and editing primitives.
- Pick the least complex primitive that fits: a Popover where a Popover belongs, not a Dialog; a plain section where a Sheet is unnecessary.

## Theming — dark by default

- Both themes ship as CSS variables under `:root` (light) and `.dark` (dark). Apply `.dark` **by default** on `<html>` via an inline script before first paint — no flash of the wrong theme.
- The toggle switches the class and persists the choice. "Follow system" may be offered as an option, but dark is the shipped default.
- Style every state in both themes. If you only checked dark, the task is not done.
- Components reference semantic tokens only (`var(--background)`, `var(--foreground)`, …). Hardcoded hex/rgb values in component code are a rejection.

## Colors — stock unless explicit

- Use the default shadcn/zinc values for: `background`, `foreground`, `card`, `popover`, `primary`, `secondary`, `muted`, `muted-foreground`, `accent`, `accent-foreground`, `destructive`, `border`, `input`, `ring`.
- No brand colors, gradients, extra chart hues, or one-off hexes unless the request explicitly specifies them.
- Meaning comes from tokens, not color invention: `destructive` for destructive actions, `muted-foreground` for secondary text, `ring` for focus.
- Text meets WCAG AA contrast against its surface in both themes.

## Typography

- Default to the system font stack (`system-ui`) unless a font is explicitly specified.
- Sizes and spacing in `rem`, so layouts scale with the user's text size.
- Size-specific tracking and leading — never one value everywhere:
  - Headings/display: tight leading (~1.05–1.2) and slight negative tracking (`-0.01em` to `-0.02em`).
  - Body: leading ~1.5, tracking ~0.
- Build hierarchy from weight + size + leading as a set; prefer a weight change over adding color.

## Layout and spacing

- 4px base grid; spacing from the 4px scale (`0.25rem` steps).
- One consistent radius scale (shadcn `--radius` and its derived sizes); no per-component radius inventions.
- Group by proximity: a control sits next to what it affects; separate groups with space before reaching for borders.
- Depth on dark: prefer borders and surface contrast over heavy shadows. Use `backdrop-filter` translucency only for floating chrome (nav bars, sheets) with content scrolling underneath — never stack translucent surfaces on each other.
- Relative units only on web: `rem` for sizes and spacing, `em` where a value scales with its own text. `px` and other absolute units ignore the user's text-size setting and are a rejection — sole exception: hairline `1px` borders/shadow offsets (the shadcn default); media-query breakpoints use `rem` too.
- Responsive by default: fluid grid/flex, percentage widths, `max-width` content columns; no horizontal scroll from phone widths through 200% zoom and the largest text-size setting.

## Interactive states — every interactive element

- **Focus**: `:focus-visible` ring (`--ring`) always visible. Never `outline: none` without a replacement indicator.
- **Press**: `:active` scale feedback `transform: scale(0.97)` with a ~100–160ms `ease-out` transition. On touch, highlight on touch-down and commit on touch-up.
- **Hover**: gated behind `@media (hover: hover) and (pointer: fine)`; near-instant color/background shift (~100–150ms, `ease`).
- **Disabled**: reduced opacity, `cursor: not-allowed`, no motion.
- **Loading**: buttons show a spinner and keep their width; skeletons match the final layout. No spinners buried inside content areas.

## Forms

- Validate inline per field (on blur/submit attempt), not only on final submit.
- Labels are always visible; placeholder text is an example, never the label.
- Error messages state what to do ("Use at least 8 characters"), never what broke internally (see `rules/content.md`).

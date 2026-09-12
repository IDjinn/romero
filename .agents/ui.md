# UI — Design System Foundations and Content Rules

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
- Error messages state what to do ("Use at least 8 characters"), never what broke internally.

## Async data — skeletons in place

- The screen mounts and renders immediately; async regions render **skeletons that match the final layout** — same rows, line counts, avatars, aspect ratios — so nothing shifts when data arrives. Gating content behind a full-page spinner until data resolves is a rejection.
- Use the shadcn/ui `Skeleton` primitive (or the React Native equivalent from the primitive set). Buttons keep showing a spinner with their width intact (see Interactive states); spinners never replace skeletons inside content areas.
- Images: when the API contract provides a placeholder, use it — a low-resolution preview or blur/dominant-color fill rendered at full size and cross-faded to the real image on load. When it doesn't, reserve the exact aspect ratio behind an image-shaped skeleton. Never a blank box that pops in, and never client-side placeholder logic that belongs in the contract.
- The skeleton pulse is opacity-only constant motion (the primitive default) — never layout properties; `prefers-reduced-motion` renders a static dimmed placeholder.

## File and folder inputs — three input paths (web/desktop)

Any UI that asks for files or folders — uploads, imports, avatars, "open project" flows — offers **all three** input paths. A picker-only input is a rejection.

- **Picker**: a real button opening the OS dialog, with `accept`/`multiple` (or folder capture) set from the API contract.
- **Drag-and-drop**: the whole target surface is the drop zone, not a small dashed box. Visible `dragover` state; dropped folders are traversed on web (`webkitGetAsEntry` / `getAsFileSystemHandle`) when folders are accepted; Electron/Tauri accept OS-level file drops.
- **Paste**: the target accepts clipboard paste of files and images (`paste` event / clipboard API) wherever the contract accepts them — screenshots included.

- One validation path for all three: type, size, and count rules are enforced identically no matter how the files arrived; failures are neutral inline messages ("PDF up to 10 MB"), never internals.
- The picker button remains the keyboard and screen-reader path — drop and paste are additions, never replacements.
- The drop-target highlight animates `border-color`/`opacity` only, ≤200ms, with reduced-motion honored (see `motion.md`).

## No backend information in the frontend

The frontend never communicates, explains, or exposes backend or internal implementation details — in UI copy, code comments, or component APIs.

- Never render things like "feature X is not enabled", "this works via service Y", "waiting for the queue", "cache invalidated", "v2 endpoint". The user cares about outcomes, not mechanisms.
- UI states derive **only** from the explicit API contract: render what the contract returns. When data is absent, render a neutral **empty / loading / error** state — never a guess about why.
- Error states say what the user can do next ("Couldn't load projects. Try again."), never what failed internally — no stack traces, service names, status codes, or infra vocabulary.
- **Raw errors never reach the UI.** "Failed to execute 'json' on 'Response': Unexpected end of JSON input" and anything like it (framework messages, stack traces, status codes) is a rejection. Catch every error, log the full detail (console/error tracking), and show the user a simple message: "Something went wrong. Try again, or contact the administrator."
- **Full error details are dev/admin only.** In development or admin builds they surface as a toast (Sonner) — never integrated into the product UI: no inline stack traces, no technical error panels, no "details" expanders for end users.
- **The business-rule test**: before writing frontend code, ask whether the information (a limit, a state, a flow, a validation) is a backend business rule. If it is, it does not belong in the frontend — it arrives through the API contract, and the frontend renders the outcome. The frontend never re-decides or re-explains business rules.
- Do not encode backend assumptions (timings, limits, feature-flag reasons, internal state machines) into frontend constants or comments. If a behavior matters to the UI, it must arrive through the contract.
- Frontend code depends on typed interfaces, not on how the backend is built. Changing backend internals must never require frontend copy or logic changes.

## Clean, simplified design

- **One primary action per view.** Secondary actions get lower emphasis; tertiary live in menus.
- **Progressive disclosure**: the common path first; advanced options one level deeper (menus, collapsibles, settings).
- **Specific labels** beat generic ones: name nav items for their contents ("Projects", "Library"), not umbrellas ("Home", "Stuff"). Specificity creates predictability.
- **Hierarchy**: order, spacing, and contrast make the most important thing the most obvious. If a control needs a paragraph of explanation, redesign the control.
- **Plain language.** No jargon, no internal codenames, concise sentences.
- Simplify density: show summaries; detail on demand. A clean screen with the right information beats a complete screen with all of it.
- Every element earns its place; when in doubt, remove it.

## Feedback

Four kinds only: **status** (ongoing), **completion**, **warning**, **error**.

- Confirm meaningful actions (toast/checkmark); expose ongoing status (spinner/progress) for anything slower than ~1 second.
- Loading content renders in place as skeletons matching the final layout (see Async data above); empty and error states replace the skeleton once the request resolves.
- File targets invite all three input paths in their copy ("Drop files here, paste, or browse"), never the picker alone.
- Validate inline, not only on submit; errors state the fix.
- Confirmation dialogs only for genuinely destructive, irreversible actions — sparingly. Overuse trains users to click through.
- Empty states include the next action ("No projects yet. Create your first one.") and never explain internals.

## Wayfinding

Every screen answers: **Where am I? Where can I go? What's here? How do I get out?** Never trap the user (see `navigation.md` for the exit requirements).

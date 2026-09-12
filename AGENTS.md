# Romero Design Rules

Design rules for frontends on **web, desktop, and mobile**. Component base: **shadcn/ui**; layout styling: **styled-components** on React/Next.js and React Native. The rules below are non-negotiable; the modules in `rules/` expand them.

**Read the module for your task before writing UI code.** Do not invent parallel rules; where these rules are silent, follow shadcn/ui defaults.

## Non-negotiables

1. **shadcn/ui first.** Build screens from shadcn/ui primitives (Dialog, DropdownMenu, Popover, Sheet, Tooltip, Command, Sonner for toasts…). Never hand-roll a complex primitive and never make a clickable `<div>` — real `<button>`/`<a>` only.
2. **Dark theme is the default; light is a toggle.** Ship both themes' CSS variables; apply `.dark` by default (inline script before paint, no flash). Verify every screen in both themes.
3. **Default palette unless explicitly overridden.** Use the stock shadcn token set (`background`, `foreground`, `card`, `popover`, `primary`, `secondary`, `muted`, `accent`, `destructive`, `border`, `input`, `ring`) with its default neutral values. No custom colors, gradients, or brand hues unless explicitly requested.
4. **Motion is gated.** Name the purpose before animating. Keyboard-initiated and 100+/day actions never animate. UI durations stay under 300ms. Animate `transform`/`opacity` only. `prefers-reduced-motion` support ships with the animation, not after.
5. **Every state has two exits.** Navigation Redundancy Rule: each screen, overlay, or mode is left via a visible affordance AND a platform convention — Escape/browser back on web; Escape, `Alt+←`, mouse side buttons on desktop; Android system back; iOS edge swipe plus a visible Back/Close button.
6. **No backend details in the frontend.** Never render or imply internal implementation state ("feature X is not enabled", "the service does Y", queue/cache/auth internals). UI states derive only from the explicit API contract; absent data renders neutral empty/loading/error states. Raw errors never reach the UI — users see a simple message directing them to retry/contact the administrator; full error details surface only as a dev/admin toast. Before writing frontend code, apply the business-rule test: if the information is a backend business rule, it does not belong in the frontend.
7. **Clean and simplified.** One primary action per view, common path first with advanced options one level deeper, specific labels over generic, plain language, visible hierarchy. If an element does not earn its place, remove it.
8. **styled-components structures the layout (React projects).** On React/Next.js, React-based desktop, and React Native, layout and custom styling are written in styled-components, defined in sibling `<Component>.styles.ts`/`.js` files — never inline in component files. Styles are responsive: `rem`/`em` only on web, flex/percentage layout with theme tokens on React Native; absolute pixel sizing is a rejection. shadcn/ui (or its React Native equivalent) still owns behavior primitives.
9. **File and folder inputs take three paths (web/desktop).** Wherever the UI asks for files or folders, drag-and-drop onto the target and clipboard paste work alongside the native picker — one shared validation path for all three. A picker-only input is a rejection.
10. **Skeletons render async data.** The page mounts before data arrives; async regions render in-place skeletons matching the final layout. Images use the backend-provided low-res preview or placeholder when the contract has one. Content is never gated behind a full-page spinner.

## Module index — read before the matching task

| Task | Read |
| --- | --- |
| Any UI: layout, colors, typography, components, theming, file inputs, loading/skeleton states | `rules/design-system.md` |
| React / Next.js app: stack, styled-components layout, style files, SSR setup | `rules/react-next.md` |
| React Native app: stack, styled-components layout, style files, theme | `rules/react-native.md` |
| Adding or changing animation, transitions, navigation feel | `rules/motion.md` |
| Building routes, screens, modals, sheets, drawers, menus | `rules/navigation.md` |
| Data fetching, lists, heavy screens (web or React Native) | `rules/platforms.md` |
| UI copy, empty/error states, feedback, destructive actions | `rules/content.md` |

## Pre-delivery checklist (condensed)

Walk the implementation, not the intention. Any "no" means not done — full version in `rules/checklist.md`.

- [ ] Every interactive element is a real control, keyboard-reachable, with a visible focus ring.
- [ ] Both themes verified (dark is the default); only stock shadcn tokens used.
- [ ] Every animation passes the gates: purpose named, ≤300ms, `transform`/`opacity` only, reduced-motion handled, no `transition: all`, no `scale(0)`, no `ease-in`.
- [ ] Every screen/overlay has ≥2 exits; Escape closes the topmost overlay and restores focus; the platform back/gesture works.
- [ ] Zero backend/internal details in UI copy or code comments; empty/loading/error states are neutral; no raw errors in the UI — simple messages (retry / contact the administrator), full details only as a dev/admin toast.
- [ ] No layout properties animated; long lists virtualized; no obvious waterfall or re-render defects.
- [ ] (React projects) All styled definitions live in `<Component>.styles.*` siblings; tokens only — no hardcoded colors, no inline style objects in component files.
- [ ] Styles are responsive: web uses `rem`/`em` only (no `px`) with fluid layout verified at breakpoints and 200% zoom; React Native uses theme scales and flex/percentage containers, no hardcoded container dimensions.
- [ ] Every file/folder input (web/desktop) accepts picker, drag-and-drop, and paste with one validation path.
- [ ] Async data renders in-place skeletons matching the final layout; images use backend previews/placeholders when the contract has one; no page gated behind a spinner.

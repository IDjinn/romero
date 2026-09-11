# Pre-Delivery Checklist

Walk the implementation, not the intention. Any "no" means the task is not done. State which items were verified and how (what you pressed, tabbed through, rendered in both themes) — "should work" is not verification.

## 1. Design system pass

- [ ] Every component is a shadcn/ui primitive or composed from them; no hand-rolled dialogs/dropdowns/sheets; no clickable `<div>`.
- [ ] Dark theme (the default) and light theme both verified on every new or changed screen.
- [ ] Only stock shadcn tokens and default palette values used; no custom colors unless explicitly requested.
- [ ] Every interactive element has a visible `:focus-visible` ring, press feedback, hover gated behind `(hover: hover) and (pointer: fine)`, plus disabled and loading states.
- [ ] Spacing on the 4px grid; consistent radius scale; layout holds at common breakpoints and with larger text sizes.
- [ ] (React projects) Every styled definition lives in a `<Component>.styles.ts`/`.js` sibling file; styled components reference tokens only (`var(--token)` on web, `theme.*` on React Native); no `StyleSheet.create` or inline style objects in component files.
- [ ] Styles are responsive: web uses `rem`/`em` only (no `px` or absolute units) with fluid layout verified at phone/tablet/desktop widths and 200% zoom; React Native uses theme scales with flex/percentage containers, no hardcoded container dimensions.

## 2. Motion pass

- [ ] Every animation has a named purpose and matches its frequency tier (keyboard/100+ per day = no animation).
- [ ] All UI durations ≤ 300ms; easing from the tokens (`--ease-out` as default); no `ease-in`; no `transition: all`.
- [ ] Only `transform`/`opacity` animated; no `scale(0)`; popover/dropdown/tooltip origins at the trigger; exits reverse their entries.
- [ ] Rapidly triggered or gesture-driven UI uses transitions/springs (interruptible), never keyframes.
- [ ] `prefers-reduced-motion` handled (gentler, not zero).

## 3. Navigation pass (per platform in the task)

- [ ] Every screen, overlay, and mode has ≥2 exits: visible affordance + platform convention. Both named.
- [ ] Escape closes the topmost overlay and restores focus; modals trap focus while open.
- [ ] Web: keyboard-only pass done (Tab order = visual order, everything operable); browser back coherent on routes and overlays; `Cmd/Ctrl+K` where a command palette exists.
- [ ] Desktop: mouse side buttons and `Alt+←/→` navigate history; Escape closes; accelerators listed in menus.
- [ ] Mobile: Android back dismisses the topmost overlay first; iOS edge swipe-back works on every pushed screen; every modal/sheet has a visible Close/Cancel/Done.

## 4. Content pass

- [ ] Zero backend/internal details in UI copy, code comments, or component APIs ("not enabled", service names, mechanisms, internals).
- [ ] Empty/loading/error states exist for all async data; wording is neutral; every error offers the next action.
- [ ] One primary action per view; common path first; labels specific; language plain.
- [ ] Confirmation dialogs only for irreversible destructive actions.

## 5. Performance pass

- [ ] Long lists virtualized; no `ScrollView` + `.map` over unbounded data (React Native).
- [ ] No waterfalls (sequential awaits of independent calls); heavy components dynamically imported; no barrel-file imports.
- [ ] No layout-property animations; gestures run through springs/worklets on the UI thread.
- [ ] Anything optimized was measured before and re-measured after.

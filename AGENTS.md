# Romero Design Rules

Design rules for frontends on **web, desktop, and mobile**: shadcn/ui components, styled-components layout on React/Next.js and React Native, dark theme by default, stock shadcn palette. Read the module for your task before writing UI code; where the modules are silent, follow shadcn/ui defaults.

## Hard rules — always

- shadcn/ui primitives only; never hand-roll dialogs/dropdowns/sheets; never a clickable `<div>`.
- Dark theme is the default; both themes styled; stock tokens only — no custom colors unless explicitly requested.
- Motion is gated: purpose named, ≤300ms, `transform`/`opacity` only, reduced-motion shipped with the animation.
- Every screen, overlay, and mode has ≥2 exits (visible affordance + platform convention).
- Zero backend details in the frontend: raw errors never reach the UI (simple message; full details only as a dev/admin toast), and backend business rules stay in the backend.
- (React projects) styled-components in sibling `<Component>.styles.ts` files; `rem`/`em` on web, theme tokens on React Native.

## Modules — read the one for your task

| Task | Read |
| --- | --- |
| Any UI: components, theming, colors, typography, states, copy, file inputs, skeletons | `.agents/ui.md` |
| React / Next.js: styled-components layout, style files, SSR, performance | `.agents/react.md` |
| React Native: layout, theme object, safe areas, performance | `.agents/react-native.md` |
| Animation, transitions, navigation feel | `.agents/motion.md` |
| Routes, screens, modals, sheets, drawers, menus | `.agents/navigation.md` |
| Verifying UI work: tooling, what to test | `.agents/testing.md` |
| Full pre-delivery checklist | `.agents/checklist.md` |

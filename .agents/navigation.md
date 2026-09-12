# Navigation — The Redundancy Rule

**Every navigation action must be reachable by two independent input methods: one visible affordance (a button) plus one platform convention (a gesture, hotkey, or hardware button).**

Corollary: **every state a user can enter, they must be able to leave.** Any screen, overlay, or mode with fewer than two exits is a defect, not a draft.

## Per-action requirements

| Action | Visible affordance (always) | Platform convention (also required) |
| --- | --- | --- |
| Go back | Back button / chevron / breadcrumb | Browser back (web), `Alt+←` and mouse back button (desktop), Android system back, iOS edge swipe |
| Go forward | Forward button where history exists | `Alt+→` (desktop), mouse forward button |
| Close / dismiss overlay | X / Close / Cancel button | Escape (web/desktop), swipe-down (sheets), click-outside, Android back |
| Commit / done | Primary button | Enter (forms), `Cmd+Enter` |
| Cancel destructive flow | Cancel button | Escape, Android back |
| Move within a widget (tabs, menus, lists) | Click / tap | Arrow keys, Tab, Home/End |
| Search / command | Visible field or icon | `Cmd/Ctrl+K`, `/` |

If you cannot name both halves for an action you just built, it is incomplete.

## Web — keyboard is the second navigation

1. Everything interactive is reachable and operable by keyboard alone; DOM order matches visual order; no positive `tabindex`, ever.
2. Focus is always visible (`:focus-visible`). Modals trap Tab while open, move focus in on open, and restore it to the invoker on close.
3. **Escape closes the topmost overlay** — dialog, dropdown, popover, command palette.
4. Browser back works: user-perceived navigation pushes real history entries; overlays that feel like pages push history too; backing out of an overlay restores the underlying state. Never `replaceState` across perceived navigation; never one history entry per keystroke.
5. `Cmd/Ctrl+K` opens the command palette/search where one exists; show hotkeys in the UI where relevant.
6. Skip-to-content link on pages with repeated navigation.

## Desktop (Electron / Tauri) — the mouse has more than three buttons

1. Mouse back/forward side buttons navigate app history. They must work; ignoring them is a rejection, and they must not fall through to another handler.
2. `Alt+←` / `Alt+→` (macOS `Cmd+[` / `Cmd+]`) do the same; wire them as accelerators and list them in the app menu.
3. Escape closes dialogs, popovers, and find-in-page; `Cmd/Ctrl+W` closes the tab/window.
4. Frameless/custom chrome: if you replace native window controls, you own Esc/close/minimize behavior and focus handling between windows.
5. `preventDefault` only on events you actually handle — global listeners that swallow events they don't act on break everything else.

## Mobile (React Native) — platform conventions don't stop at the abstraction layer

1. **Android**: back dismisses the topmost overlay first (sheet, dialog, drawer, search mode) — never the screen behind it; never exit the app from behind an open overlay. Handle with `BackHandler` (subscribe on mount, unsubscribe on unmount, return `true` only when the event is consumed); keep the callback disabled when not needed. Opt in to predictive back and verify the preview animations don't break the layout.
2. **iOS**: every screen has a visible way back. Keep edge swipe-back working — don't break it with hidden nav bars or custom back buttons without a replacement. Every modal and sheet has a visible Close/Cancel/Done; swipe-to-dismiss is the second exit, never the only one.
3. Keep native-stack gestures enabled; don't cover the screen edge with opaque views.
4. Overlays close by gesture AND button AND (Android) back — configure the library's `onBackButtonPress`/backdrop props explicitly instead of assuming defaults.

## Anti-patterns — automatic rejections

- Clickable `<div>`/`<span>` without role, tabindex, and key handling.
- `outline: none` with no replacement indicator; any `tabindex` > 0.
- Modal missing any of: visible close button, Escape handler, focus trap, focus restore.
- Overlay closable by outside-click only — no button, no Escape.
- Empty or swallow-all back handler; back exiting the app while an overlay is open.
- SPA breaking browser back: `replaceState` on perceived navigation, a history entry per keystroke, hash changes without state sync.
- Any screen, overlay, or mode with fewer than two exits.

## Verify before done

1. List every screen and overlay; name its two exits.
2. Tab through the flow: every control reachable and operable, visible focus, DOM order = visual order.
3. Escape closes the topmost overlay everywhere; focus lands back on the invoker.
4. Browser/app back lands where a user would expect on every state change (routes, opened overlays, filters).
5. Android: gesture and 3-button back dismiss the topmost overlay first.
6. iOS: edge swipe-back works on every pushed screen; every modal/sheet has a visible exit.
7. Desktop: side buttons and `Alt+←/→` navigate; Escape closes.
8. Deep links / cold start at any screen leave sane back behavior — no dead ends, no crash-to-root.

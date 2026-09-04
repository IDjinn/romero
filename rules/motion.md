# Motion — Animation and Transitions

Motion exists to make navigation fluid: it shows where things came from and went, confirms the interface heard the user, and bridges state changes. Everything else is cut.

## Gate 1 — Should this animate at all?

| Frequency | Decision |
| --- | --- |
| 100+ times/day (command palette, keyboard shortcuts, primary nav toggle) | **No animation. Ever.** |
| Tens of times/day (hover effects, list navigation) | Near-imperceptible only, or nothing |
| Occasional (modals, sheets, toasts, page transitions) | Standard animation |
| Rare / first-time (onboarding, success, celebration) | Delight allowed here |

Keyboard-initiated actions are a disqualifier, not a judgment call.

## Gate 2 — Name the purpose

One of: **feedback** (the UI heard the user), **spatial consistency** (where it came from / went), **state indication**, **preventing a jarring change**. "It looks cool" on a frequently-seen element is a reason to stop. Data the user is reading or acting on never moves for style.

## Tokens — define once, use everywhere

```css
:root {
  --ease-out: cubic-bezier(0.23, 1, 0.32, 1);      /* entrances, exits — default */
  --ease-in-out: cubic-bezier(0.77, 0, 0.175, 1);  /* on-screen movement, morphs */
  --ease-drawer: cubic-bezier(0.32, 0.72, 0, 1);   /* sheets, drawers (iOS-like) */
  --duration-fast: 150ms;
  --duration-base: 200ms;
  --duration-slow: 300ms;
}
```

Built-in `ease-out` is too weak for deliberate animations — use the tokens. **Never `ease-in` on UI**: it delays the exact moment the user is watching.

| Situation | Easing |
| --- | --- |
| Entering / exiting | `--ease-out` |
| Moving / morphing on screen | `--ease-in-out` |
| Hover / color change | `ease` |
| Constant motion (progress, marquee) | `linear` |

## Durations — under 300ms

| Element | Duration |
| --- | --- |
| Button press feedback | 100–160ms |
| Tooltips, small popovers | 125–200ms |
| Dropdowns, selects | 150–250ms |
| Modals, sheets, page transitions | 200–300ms |
| Marketing / explanatory | May be longer |

**Asymmetric timing rule**: slow where the user is deciding (hold-to-confirm: 2s linear), snappy where the system responds (release: 200ms `--ease-out`). Exit may be slightly faster than enter.

## Properties and physicality

- Animate **`transform` and `opacity` only** (`clip-path` is sanctioned). Never `width`/`height`/`margin`/`padding`/`top`/`left` — `height` is tolerated only for accordions.
- **Never `scale(0)`** — enter from `scale(0.95)` + `opacity: 0`. Nothing appears from nothing.
- **`transform-origin` at the trigger** for popovers, dropdowns, menus, tooltips. Modals are exempt — they stay centered.
- Use percentage translates (`translateY(100%)`) over hardcoded pixels.
- **Exit the way it entered**: a sheet that slides in from the bottom dismisses to the bottom.
- Stagger group entrances 30–80ms per item; never block interaction during a stagger.

## Springs and gestures

- Springs for anything gesture-driven or interruptible: drags with momentum, sheets, elements that should feel alive.
- Default critically damped, no bounce: `{ type: "spring", bounce: 0, duration: 0.4 }`. Bounce 0.1–0.3 only when the gesture itself carried momentum (flick, throw, drag-to-dismiss).
- **Velocity handoff**: a drag's release velocity becomes the spring's initial velocity — no seam between dragging and animating.
- **Interruptibility**: always animate from the current on-screen value, never the target. Use CSS transitions (they retarget) or springs — never keyframes for rapidly triggered or gesture-driven motion.
- Rubber-band at boundaries (progressive resistance), never hard stops.

## Tooling — cheapest that works

| Need | Tool |
| --- | --- |
| Hover, press, color, class/attribute toggle | CSS transition |
| Entry animation on mount | CSS `@starting-style` |
| Predetermined motion that must survive page load | CSS animation (runs off main thread) |
| Programmatic control with CSS performance | WAAPI (`element.animate()`) |
| Springs, layout/exit animations, gesture-driven values | Motion (motion.dev) |

- Don't install a motion library for a fade. Don't hand-roll a component (toast, drawer, command menu) that shadcn/ui already ships.
- In Motion, use the full transform string (`transform: "translateX(100px)"`), not the `x`/`y`/`scale` shorthands — the shorthands are not hardware-accelerated and drop frames under load.
- Never drive a child's transform from a CSS variable on the parent (style recalc storm); set `transform` on the element itself.
- React Native: run animations in Reanimated worklets (UI thread); never JS-driven timing for gestures.

## Accessibility — ships with the animation

```css
@media (prefers-reduced-motion: reduce) {
  .element { animation: fade 0.2s ease; } /* keep opacity/color, drop transform motion */
}

@media (hover: hover) and (pointer: fine) {
  .element:hover { transform: scale(1.05); }
}
```

- Reduced motion = **gentler, not zero**: keep opacity/color transitions that aid comprehension; drop movement and position changes.
- Hover motion is always gated behind `(hover: hover) and (pointer: fine)`.
- Theme switches (dark ↔ light) cross-fade smoothly; no abrupt brightness jumps.

## Never ship (automatic rejection)

| Never | Instead |
| --- | --- |
| `transition: all` | Name the exact properties |
| `scale(0)` entrance | `scale(0.95)` + `opacity: 0` |
| `ease-in` on UI | `--ease-out` token |
| Built-in `ease-out` on a deliberate animation | `--ease-out` token |
| Animation on a keyboard/high-frequency action | No animation |
| UI duration > 300ms without a reason | 150–250ms |
| `transform-origin: center` on a trigger-anchored popover | Trigger origin (modals exempt) |
| Keyframes on toasts/toggles/rapidly triggered UI | CSS transitions |
| Animating layout properties | `transform` / `opacity` |
| Motion `x`/`y`/`scale` under load | Full transform string |
| Ungated `:hover` motion | Hover media query |
| Missing `prefers-reduced-motion` | Gentler variant, not zero |
| Everything entering at once | 30–80ms stagger |

# Platforms — Performance Rules That Affect UX

Motion and polish stop at 60fps. These are the performance rules to apply while building UI. Anything not covered here follows **measure → optimize → re-measure**: profile the target interaction first, revert if metrics don't improve, and never add memoization without a measured problem.

## Web / Next.js

- **No waterfalls**: fire independent operations in parallel (`Promise.all`); move `await`s into branches that actually use them; check cheap sync conditions before awaiting flags; start promises early and await late.
- **Bundle size**: import directly from source modules, never through barrel files; dynamically import heavy components (editors, charts, maps); load analytics and third-party scripts after hydration; preload on hover/focus for perceived speed.
- **Server → client**: minimize serialized props — pass client components only what they render; authenticate server actions like API routes.
- **Re-render hygiene**: derive state during render instead of syncing it in effects; never define components inside components; subscribe to derived booleans, not raw values; `useDeferredValue` for expensive typing-driven renders; `startTransition` for non-urgent updates.
- Suspense boundaries around slow regions so the page stays mounted and interactive around them; skeletons match the final layout (`rules/design-system.md`); `content-visibility` for long lists.

## React Native (mobile)

- **Lists**: FlatList/FlashList (or Legend List) for anything long — a `ScrollView` with mapped unbounded data is a rejection.
- **Profile before optimizing**: React Native DevTools Profiler on the target interaction. No speculative memoization; no flagging stale closures without a shown read path.
- **Startup**: native navigation (`react-native-screens`); measure TTI on cold starts only; preload commonly-used expensive screens.
- Search/filter `TextInput` stays uncontrolled (or carefully isolated) so keystrokes don't re-render the screen.
- Animations run in Reanimated worklets (UI thread) — never JS-driven `Animated` timing for gestures.
- Bottom sheets come from an optimized GestureHandler/Reanimated-based library; keep re-renders out of the drag path.
- Avoid barrel imports; keep Hermes enabled; check a dependency's size before adding it.

## Perceived performance (all platforms)

- A fast spinner reads as faster loading; spinners appear immediately.
- Once one tooltip is open, adjacent tooltips open instantly — skip both the delay and the animation.
- Optimistic UI for actions that almost always succeed; reconcile on the response.
- Preload the likely next screen on hover/focus intent where the platform allows.
- Images render from a backend-provided low-res preview or placeholder (or a reserved aspect-ratio skeleton) and cross-fade in — no blank boxes, no layout shift.

# React Native — Project Rules

Entry module for React Native apps (Expo or bare workflow). **styled-components/native structures all layout and appearance**; behavior primitives come from the shadcn-equivalent set (`ui.md`). All global rules apply unchanged — this module adds the stack, file, and theme conventions.

## Division of labor

- Complex behavior — dialogs, menus, bottom sheets, pickers, toasts — comes from the project's shadcn-equivalent primitive set (e.g. react-native-reusables) or an established library. Never hand-rolled (non-negotiable 1).
- styled-components/native owns layout and appearance: screens, sections, stacks, rows, surfaces, typography.
- Interactive elements are real controls: `Pressable`, `TextInput`, `Switch`, links via the navigation library. A `View` acting as a button is a rejection; a custom control built on `Pressable` sets `accessibilityRole="button"`, the matching `accessibilityState`, and visible pressed/disabled states.

## Style files — `<Component>.styles.ts`

- Same convention as web: `TaskCard.tsx` + `TaskCard.styles.ts`, sibling files, styles imported from the styles file:

```ts
// TaskCard.styles.ts
import styled from "styled-components/native";

export const Card = styled.View`
  padding: ${({ theme }) => theme.space[4]}px;
  background: ${({ theme }) => theme.colors.card};
  border-radius: ${({ theme }) => theme.radius.md}px;
`;

export const Title = styled.Text`
  font-size: ${({ theme }) => theme.type.size.md}px;
  font-weight: ${({ theme }) => theme.type.weight.semibold};
  color: ${({ theme }) => theme.colors.foreground};
`;
```

The `px` suffix is styled-components' React Native convention: it is stripped before the style reaches React Native, leaving a unitless density-independent number. There are no CSS units on native — every dimension is a dp number, which is exactly why no raw number may appear in a styles file; sizes come from theme scales only.

- Styles files contain styled definitions and local `css` helpers only — no JSX, no hooks, no logic, no event handlers.
- styled-components is the single styling mechanism: no `StyleSheet.create`, no inline `style` objects, no second styling library. Exceptions: Reanimated animated values (`useAnimatedStyle` — motion, not styling) and one-off measured dimensions.
- Platform differences (`Platform.select`, `Platform.OS`) live in styles files or the theme — never scattered through component JSX.
- Styled-only props use the transient `$` prefix.

## Theming — token object, dark default

- React Native has no CSS variables: the theme is a plain object using the same shadcn token names (`background`, `foreground`, `card`, `primary`, `secondary`, `muted`, `mutedForeground`, `accent`, `destructive`, `border`, `input`, `ring`). Values are the default zinc palette converted to `#rrggbb` — no custom palette unless explicitly requested.
- One `theme.ts` exports `darkTheme` (default), `lightTheme`, `space` (4-point grid scale), `radius`, `type` (font size/weight scale), and the font stack. styled-components' `ThemeProvider` wraps the app; components read tokens via `${({ theme }) => …}` or `useTheme()` — never through screen-level color conditionals.
- Dark ships as the default; the toggle persists the choice and it is resolved before the first screen renders (during splash/startup) — no wrong-theme flash on cold start.
- Toggling switches primitives and styled components together (one theme object). Both themes verified on every screen.

## Layout

- Flexbox; screen structure uses dimensionless `flex`; spacing from `theme.space` (4px grid); radius from `theme.radius`; typography hierarchy from the theme (weight + size), system font by default.
- Safe areas via `react-native-safe-area-context`; insets reach styled components as props — never hardcode status-bar/notch dimensions.
- `KeyboardAvoidingView` (or the library equivalent) on every screen with inputs; the primary action stays reachable with the keyboard open.
- Never disable `allowFontScaling`; layouts hold at larger text sizes.
- Long lists use FlatList/FlashList (see Performance) — styled components style the rows; a `ScrollView` mapping unbounded data stays a rejection.

## Responsiveness — adaptive, not fixed

- React Native has no `rem`: every dimension is a density-independent dp number. That is not a license for absolutes — a hardcoded dp size is still a fixed measure that breaks across devices. Containers are never sized in absolute dp: `flex`, percentage widths, `flexWrap`, and content-driven height.
- `useWindowDimensions` drives adaptive layout (columns collapse to stacks, side panels become sheets). Phones, tablets, and both orientations must render usable layouts — not stretched phone layouts.
- Spacing, radius, and font sizes come only from the theme scales. Sanctioned exceptions for raw numbers: `StyleSheet.hairlineWidth`, intrinsic icon/image sizes, and values measured at runtime (`onLayout`).
- Verify with the largest accessibility text sizes (`allowFontScaling` stays on) and on the smallest supported device: no clipped text, no overflow, primary actions still reachable.

## Navigation, motion, states

- Navigation follows `navigation.md` (mobile section): Android hardware/predictive back dismisses the topmost overlay first; iOS edge swipe stays intact; every screen, modal, and sheet carries a visible Back/Close affordance.
- Motion follows `motion.md`; gestures animate through Reanimated worklets on the UI thread; check `AccessibilityInfo.isReduceMotionEnabled` and ship the reduced variant with the animation.
- Empty/loading/error states follow `ui.md` — neutral wording, next action offered, zero internals. Async regions render skeletons matching the final layout (the primitive set's `Skeleton`); images cross-fade from a backend-provided preview when the contract has one.

## Performance

- Profile before optimizing: React Native DevTools Profiler on the target interaction. No speculative memoization; no flagging stale closures without a shown read path; anything optimized was measured before and re-measured after.
- Startup: native navigation (`react-native-screens`); measure TTI on cold starts only; preload commonly-used expensive screens.
- Search/filter `TextInput` stays uncontrolled (or carefully isolated) so keystrokes don't re-render the screen.
- Bottom sheets come from an optimized GestureHandler/Reanimated-based library; keep re-renders out of the drag path.
- Avoid barrel imports; keep Hermes enabled; check a dependency's size before adding it.

## Anti-patterns — automatic rejections

- Styled definitions, `StyleSheet.create`, or inline styles inside component files.
- Color literals outside `theme.ts`.
- `View` with a press handler and no `accessibilityRole`; interactive control without pressed/disabled states.
- Wrong theme flash on cold start; a screen styled in only one theme.
- Two styling systems mixed in one project.
- Raw dp numbers in a `.styles` file outside theme tokens (except `hairlineWidth`, intrinsic media sizes, measured values).
- Hardcoded container dimensions: fixed width/height that overflows small devices or stretches empty space on tablets.

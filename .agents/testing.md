# Testing — Verifying UI Work

Testing proves the rules in these modules hold on real renders — not coverage numbers. Test behavior through the API contract; never test implementation details. How each checklist item is verified lives here; the gate itself is `checklist.md`.

## What every UI task verifies

- **Real controls and semantics**: interactive elements render as `<button>`/`<a>`/`<input>` (web) or role-carrying controls (React Native); keyboard operability — tab order matches visual order, focus ring visible, Escape closes the topmost overlay and restores focus.
- **Both themes**: dark (default) and light rendered and compared on every new or changed screen; a hardcoded color shows up as a theme diff.
- **All data states**: loading (skeletons in place, page mounted underneath), empty, error, success. Errors render the simple user message ("Try again, or contact the administrator.") — assert that raw errors ("Failed to execute 'json'…", stack traces, status codes) never appear in the DOM and the full detail only goes to logs.
- **Exits**: every screen and overlay closes two ways — visible affordance and platform convention (Escape/browser back on web, hardware back on Android, edge swipe on iOS).
- **Motion**: UI durations ≤300ms, `transform`/`opacity` only, and `prefers-reduced-motion` renders the gentler variant.
- **Responsiveness**: web at phone/tablet/desktop widths and 200% zoom; React Native on phone/tablet and both orientations, with the largest accessibility text sizes.

## Tooling — default stack

| Layer | Web / Next.js | React Native |
| --- | --- | --- |
| Unit + component | Vitest + React Testing Library | Jest + React Native Testing Library |
| E2E / flows | Playwright | Detox or Maestro |
| Theme + responsive review | Playwright screenshots per breakpoint and zoom level | Screenshots per device and orientation |

- Mock at the **API contract boundary** (e.g. MSW on web; contract-typed handlers on native). Tests never see backend internals — same rule as the UI itself.
- Query by role and accessible name — the same tree assistive technology uses. Test ids only where no accessible name exists.
- Deterministic tests: fake timers and clocks; disable or shorten animation durations globally, and keep explicit assertions for reduced-motion output.
- User-path tests over unit exhaustiveness: one E2E per critical flow (load → interact → save → error → recover) beats dozens of isolated component tests.

## What not to test (rejections)

- Snapshot-everything suites; tests over internal state, private functions, or render counts with no user-visible behavior behind them.
- Coverage targets for their own sake — every test maps to a rule in these modules or a user path.
- Mocks that re-implement backend business logic (the business-rule test applies to test fixtures too).
- Flaky tests shipped "temporarily": fix or delete them in the same task.

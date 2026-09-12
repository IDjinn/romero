# Content — Backend Separation, Clean Design, UI Copy

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
- Loading content renders in place as skeletons matching the final layout (`rules/design-system.md`); empty and error states replace the skeleton once the request resolves.
- File targets invite all three input paths in their copy ("Drop files here, paste, or browse"), never the picker alone.
- Validate inline, not only on submit; errors state the fix.
- Confirmation dialogs only for genuinely destructive, irreversible actions — sparingly. Overuse trains users to click through.
- Empty states include the next action ("No projects yet. Create your first one.") and never explain internals.

## Wayfinding

Every screen answers: **Where am I? Where can I go? What's here? How do I get out?** Never trap the user (see `rules/navigation.md` for the exit requirements).

# React / Next.js — Project Rules

Entry module for React and Next.js (App Router) apps. **styled-components structures the layout**; shadcn/ui keeps owning behavior primitives (`rules/design-system.md`). All global rules apply unchanged — this module adds the stack, file, and SSR conventions.

## Division of labor

- **shadcn/ui owns behavior**: Dialog, DropdownMenu, Popover, Sheet, Command, Sonner… Never rebuild a primitive with styled components — that violates non-negotiable 1 just as much as hand-rolling it.
- **styled-components own layout and appearance**: page shells, sections, grids, stacks, spacing, surfaces, typography. When a component's JSX fills with nested wrappers or utility-class soup, extract a styled component instead.
- Adjusting a shadcn primitive: prefer its props and variants (`size`, `variant`); `styled(Button)` is the escape hatch for genuine one-offs, not the default path.

## Style files — `<Component>.styles.ts`

- Every styled definition lives in a sibling file named `<Component>.styles.ts` (TypeScript) or `<Component>.styles.js` (JavaScript): `TaskCard.tsx` + `TaskCard.styles.ts` — same folder, same base name. The extension matches the project language; styles files contain no JSX.
- The component file reads as structure; the styles file reads as appearance:

```tsx
// TaskCard.tsx — structure
import { Card, Title, Meta } from "./TaskCard.styles";

export function TaskCard({ task }: TaskCardProps) {
  return (
    <Card>
      <Title>{task.title}</Title>
      <Meta>Due {task.dueLabel}</Meta>
    </Card>
  );
}
```

```ts
// TaskCard.styles.ts — appearance
import styled from "styled-components";

export const Card = styled.section`
  padding: 1rem;
  background: var(--card);
  border: 1px solid var(--border);
  border-radius: var(--radius);
`;

export const Title = styled.h3`
  font-weight: 600;
  color: var(--foreground);
`;

export const Meta = styled.p`
  font-size: 0.875rem;
  color: var(--muted-foreground);
`;
```

- Styles files contain `styled.*` definitions, keyframes, and small local `css` helpers only — no JSX, no hooks, no data fetching, no event handlers.
- No styled definitions inside component files, and no inline `style={{}}` except truly dynamic runtime values (measured sizes, drag offsets) — anything repeated or structural goes to the styles file.
- Styled-only props use the transient `$` prefix (`$active`, `$tone`) so they never reach the DOM.
- Helpers shared across files (focus ring, scrollbars) live in one shared styles module — never copied between `.styles` files.

## Theming — one token system

- The shadcn CSS variables (`:root` / `.dark`) remain the single source of truth (non-negotiable 3). Styled components consume them as `var(--token)` — no hex/rgb literals, no parallel JS color object.
- No styled-components `ThemeProvider` on web: the `.dark` class toggle (non-negotiable 2) switches shadcn primitives and styled components together, for free.
- Motion inside styled components uses the `rules/motion.md` tokens, e.g. `transition: transform var(--duration-base) var(--ease-out);` — never literal eases, durations, or `transition: all`.

## Responsiveness — relative units only, fluid by default

- Web styles use relative units only: `rem` for sizes and spacing, `em` where a value must scale with its own text. **No `px`, `pt`, or any absolute unit in a `.styles` file** — absolute sizes ignore the user's text-size setting and break zoom. Sole exceptions: hairline `1px` borders and shadow offsets (they must not scale with text — the shadcn default) and `0` values.
- Layouts are fluid: `flex` and grid with `fr` tracks, percentage widths, `max-width` + inline padding for content columns. A component with a fixed width that overflows a small viewport or floats in a huge one is a defect.
- Breakpoints are mobile-first media queries in `rem` (so they respond to text-size settings too), defined once as shared `css` helpers in the shared styles module — never magic numbers scattered across files.
- Verify every screen at phone/tablet/desktop widths, at 200% browser zoom, and with the largest text-size setting: no horizontal scroll, no clipped or overlapping content.

## Next.js specifics

- App Router; pages and layouts are server components by default. styled components are client-side: any file that defines or imports them is a client component (`'use client'`). Keep pages on the server by isolating styled subtrees in client components.
- Enable once in `next.config`: `compiler: { styledComponents: true }` — readable class names, SSR support, dead-code elimination.
- Mount the SSR registry once in the root layout using the official pattern: a client registry component with `ServerStyleSheet` + `StyleSheetManager` + `useServerInsertedHTML`. Verify with a hard reload under network throttling: no flash of unstyled content.
- Route-level `loading.tsx` renders skeletons in the route's final layout — the shell stays mounted, never a bare spinner page; `error.tsx` and `not-found.tsx` provide the other neutral states from `rules/content.md`; data-fetching and bundle rules live in `rules/platforms.md`.

## Anti-patterns — automatic rejections

- Styled definitions or inline `style={{}}` objects inside a component file.
- Hex/rgb color literals in a `.styles` file.
- A dialog, dropdown, sheet, or command palette rebuilt from styled components instead of the shadcn primitive.
- FOUC on first paint (missing SSR registry or missing dark-theme script).
- Two token systems: CSS variables for primitives, a JS color object for styled components.
- `px` (or any absolute unit) in a web `.styles` file.
- Fixed-width layout: horizontal overflow at common breakpoints, at 200% zoom, or with larger text sizes.

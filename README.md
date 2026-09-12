# Romero Design Rules

Agent-agnostic design rules for frontends — **web, desktop, and mobile**. Plain Markdown, no tool-specific syntax: any AI coding agent (ZCode, Cursor, Codex, Claude Code, Copilot, …) consumes them by reading `AGENTS.md`.

## What this package enforces

- **shadcn/ui as the component base.** Use the primitives; never hand-roll dialogs, dropdowns, sheets, or command menus.
- **styled-components for layout** on React/Next.js and React Native — styles live in sibling `<Component>.styles.ts` files; shadcn/ui (or its React Native equivalent) keeps owning behavior primitives.
- **Dark theme by default**, light theme via toggle. Both are always styled.
- **Default shadcn palette** (neutral/zinc) unless colors are explicitly requested.
- **Purposeful motion for fluid navigation**: every animation passes a frequency and purpose gate, stays under 300ms, animates only `transform`/`opacity`, and ships with reduced-motion support.
- **Redundant navigation**: every state is exitable two ways — a visible affordance plus the platform convention (browser back, Escape, Android back, iOS edge swipe, mouse side buttons).
- **Zero backend leakage**: the UI never explains or exposes backend/internal implementation details; raw errors never reach the UI (simple messages; full details only as a dev/admin toast); backend business rules are never re-implemented in the frontend.
- **Three-path file inputs on web/desktop**: wherever the UI asks for files or folders, drag-and-drop and clipboard paste work alongside the native picker.
- **Skeletons for async data**: pages mount before data arrives; async regions render skeletons matching the final layout, and images cross-fade from backend-provided previews when available.
- **Clean, simplified design**: hierarchy, progressive disclosure, specific labels, plain language.

## How to adopt

1. **Point your agent at this folder** — `AGENTS.md` is the entry point: hard rules plus a router to the module for the task, keeping agent context small.
2. **Or copy `AGENTS.md` + `.agents/` into a project root** — they work standalone.
3. Agents load only the module relevant to the task (see the index in `AGENTS.md`).

## Files

| File | Purpose |
| --- | --- |
| `AGENTS.md` | Entry point: hard rules and module router |
| `.agents/ui.md` | shadcn/ui foundations: theming, colors, typography, states, content rules, file inputs, skeletons |
| `.agents/react.md` | React/Next.js: styled-components layout, `.styles` files, SSR, performance |
| `.agents/react-native.md` | React Native: styled-components/native layout, theme object, safe areas, performance |
| `.agents/motion.md` | Animation gates, tokens, per-platform tooling, never-ship list |
| `.agents/navigation.md` | Redundancy Rule and per-platform navigation obligations |
| `.agents/testing.md` | Verifying UI work: tooling, what to test, rejections |
| `.agents/checklist.md` | Pre-delivery gate |

## Sources

Synthesized from: `navigation-tools`, `animate`, `review-animations`, `emil-design-eng`, `apple-design`, `react-native-best-practices`, `vercel-react-best-practices`.

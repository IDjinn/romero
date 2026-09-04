# Romero Design Rules

Agent-agnostic design rules for frontends — **web, desktop, and mobile**. Plain Markdown, no tool-specific syntax: any AI coding agent (ZCode, Cursor, Codex, Claude Code, Copilot, …) consumes them by reading `AGENTS.md`.

## What this package enforces

- **shadcn/ui as the component base.** Use the primitives; never hand-roll dialogs, dropdowns, sheets, or command menus.
- **Dark theme by default**, light theme via toggle. Both are always styled.
- **Default shadcn palette** (neutral/zinc) unless colors are explicitly requested.
- **Purposeful motion for fluid navigation**: every animation passes a frequency and purpose gate, stays under 300ms, animates only `transform`/`opacity`, and ships with reduced-motion support.
- **Redundant navigation**: every state is exitable two ways — a visible affordance plus the platform convention (browser back, Escape, Android back, iOS edge swipe, mouse side buttons).
- **Zero backend leakage**: the UI never explains or exposes backend/internal implementation details. Frontends depend only on explicit API contracts.
- **Clean, simplified design**: hierarchy, progressive disclosure, specific labels, plain language.

## How to adopt

1. **Point your agent at this folder** — `AGENTS.md` is the entry point and is self-contained for the critical rules.
2. **Or copy `AGENTS.md` + `rules/` into a project root** — they work standalone.
3. Agents load only the module relevant to the task (see the index in `AGENTS.md`).

## Files

| File | Purpose |
| --- | --- |
| `AGENTS.md` | Entry point: non-negotiables, module index, condensed checklist |
| `rules/design-system.md` | shadcn/ui foundations: theming, colors, typography, states |
| `rules/motion.md` | Animation gates, tokens, per-platform tooling, never-ship list |
| `rules/navigation.md` | Redundancy Rule and per-platform navigation obligations |
| `rules/platforms.md` | Performance rules that affect UX (web/Next.js, React Native) |
| `rules/content.md` | Backend-leak prohibition, clean design, UI copy and feedback |
| `rules/checklist.md` | Pre-delivery gate: five pass/fail checks |

## Sources

Synthesized from: `navigation-tools`, `animate`, `review-animations`, `emil-design-eng`, `apple-design`, `react-native-best-practices`, `vercel-react-best-practices`.

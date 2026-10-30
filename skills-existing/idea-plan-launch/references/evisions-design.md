# evisions app design — the essentials for building on-brand

Enough of the evisions **internal-app** design system (Petr's DESIGN-MANUAL v2.6) to make an internal app or dashboard look like evisions from day one.

> **Authoritative source: the `ai-evisions/app-design-system` repo.** Vendor its `tokens.css` + `fonts/` + `tailwind-preset.js` into your app; full component recipes live in `DESIGN-MANUAL.html` there, rollout steps in `IMPLEMENTATION-GUIDE.md`. Brand primitives (the identity layer) are `ai-evisions/brand-tokens` (human source `01_AI Context/02_Brand/visual-guidelines.md`). This file is the travel-size summary; the repo wins if they disagree.

There are **two design systems** — pick one, never mix:
- **Internal apps** (dashboards, admin tools, surveys) → this reference + `app-design-system`.
- **Client-facing / public web** (marketing, client portals) → ask the AI team for the public-web doc; same brand, different typography/density.

## Rule #1 — read tokens, never hardcode

Every value (colors, radii, greys, shadows, fonts) lives in `tokens.css`. Apps read **`var(--token)`** (through the Tailwind preset) — **no app defines its own hex or radius.** Hardcode a value locally and the system fragments into five dialects. If a value doesn't fit, change `tokens.css` in the repo (and tell Petr), not the app.

Wire it (these are **vendored copies — not an npm package**): copy `tokens.css` in and `@import` it first; copy `fonts/` to `public/fonts/`; copy `tailwind-preset.js` in and reference it locally, `presets: [require("./tailwind-preset")]`. Then use `bg-accent`, `text-text-secondary`, `rounded-md`, `shadow-sm`, `font-mono`, etc. — all resolve to tokens. Update = re-copy the files from the repo (there is intentionally no shared package to depend on).

## Colors (all via tokens)

- **Accent** `--accent #E5712C` — the only orange (active states, CTAs). **Text on any orange/accent surface is WHITE** (Petr 2026-07-08). `--accent-hover`, `--accent-subtle`.
- **Warm neutrals** `--bg-canvas #FBFAF8`, `--bg-surface`, `--bg-muted`, `--border`. **No cold greys** (`slate-*`, `#9CA3AF`).
- **Text** `--text-primary / -secondary / -tertiary / -disabled`. **Dark shell** `--shell-bg` etc. (sidebar/topbar).
- **Semantic** `--success / --warning / --danger / --info` (+ `-bg` tints + `-text` for AA contrast). Blue **only** as `--info` (state), never a category.
- **Viz** `--viz-1..5` — warm brand palette for charts / category tags (not a rainbow).

## Typography (self-hosted, no CDN)

- `--font-sans` **Inter Tight** — UI/body (brand-legal substitute for PP Neue Montreal, which has no webfont license; do **not** use Arial/Inter/Roboto/system-ui).
- `--font-serif` **Lora** — leads / subheads only.
- `--font-mono` **JetBrains Mono** — all data numbers (KPI, amounts, counts).
- Sizes: the `--fs-*` scale (`--text-*` is deprecated).

## Layout / shell

Dark sidebar (`--shell-bg`) with an inset orange active bar; sticky **opaque** topbar with the `--gradient-hairline` (2px) directly under it — never a fill. Warm canvas content. Cards: `bg-surface` + **shadow-as-border** (`--shadow-sm`), no 1px borders. Light + dark themes (`[data-theme="dark"]`); tokens flip, components don't change.

## Key components (recipes in DESIGN-MANUAL.html)

- **KPI card:** eyebrow (uppercase, tertiary) + big **mono** number + delta/meta chip.
- **Buttons:** primary = solid `--accent`, CTA = `--gradient-cta` (max 1/screen) — both **white text**; secondary = surface + shadow + dark text; danger = `--danger` + white.
- **Filter pill** active = solid `--accent` + white text; rest = surface + shadow.
- **Icons:** Lucide, pinned (`lucide-react`), stroke-2/currentColor/24. The brandmark is not an icon (`brandmark.svg`).

## Anti-patterns (Petr rejected)

Rainbow category colors, cold greys, blue as a category, glow on cards, disabled focus rings, **dark** text on orange, hardcoded hex, a shifted-viewBox brandmark.

## Reference implementation

`ai-evisions/c-level-product-mapping` (Product Mapping) — the first fully-conformant app; its `src/app/tokens.css` is the verbatim v2.6 that seeds the repo. Ask the AI team for the full design doc if you need detail beyond this.

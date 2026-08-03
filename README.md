# cognativ-testbed

A whitelabel Next.js testbed for building and previewing app screens at pixel-accurate dimensions. Fork it, drop a component in, see it inside the right device frame immediately — no routing, no backend, no state management overhead.

## What it does

- **Viewport shell** — renders your screen component inside a device frame (iPhone 17 Pro Max, Android Large, Responsive, or full Desktop)
- **Floating dev menu** — toggle platform, switch viewport presets, and fine-tune zoom scale without touching code
- **Motion system** — a portable set of CSS animation classes (`ms-enter`, `ms-btn`, `ms-checkbox`, etc.) ready to apply
- **Clean starting point** — one file to edit, one token block to rebrand, zero opinions about your screen's architecture

## Stack

| | |
|---|---|
| Framework | Next.js 16.2.1 (Pages Router, Turbopack) |
| Language | TypeScript 5 |
| UI | React 19 |
| Styling | Tailwind CSS 4 + inline styles |
| Font | Inter via `next/font/google` |

## Forking for a new prototype

```bash
gh repo create my-prototype --private --template AgustinBallesteros/cognativ-testbed
cd my-prototype
npm install
npm run dev        # → http://localhost:3000
```

No template on the repo yet? Clone and re-point the remote:

```bash
git clone https://github.com/AgustinBallesteros/cognativ-testbed.git my-prototype
cd my-prototype && rm -rf .git && git init
```

### Let the agent configure it

Open the fresh fork in Claude Code and it will notice the unconfigured state (`.claude/TEMPLATE`),
ask for the project name, what you're building, platform focus, and accent color, then apply them
across every file. Run it explicitly any time with:

```bash
/new-prototype
```

It finishes by deleting `.claude/TEMPLATE`, the `/new-prototype` command, and the first-run block in
`CLAUDE.md`, so the fork stops behaving like a template.

### Or do it by hand

1. `package.json` → set `name`
2. `styles/globals.css` → set `--accent` (and `--shell-bg` / `--frame-bg` if the canvas isn't white)
3. `public/favicon.svg` → recolor the placeholder mark
4. `pages/index.tsx` → replace the placeholder with your screen
5. `README.md` / `ONBOARDING.md` → retitle, and drop this forking section
6. Delete `.claude/TEMPLATE`

That's the whole rebrand. Nothing else carries project-specific content.

## Adding a screen

Open `pages/index.tsx`. Find the placeholder inside the viewport frame:

```tsx
{/* ── Drop your screen component here ── */}
<span style={{ color: "#ccc", fontSize: 13, fontFamily: "var(--font-inter)" }}>
  {isDesktop ? "Desktop" : "Mobile"} — add your screen here
</span>
```

Replace it with your component:

```tsx
{isDesktop ? <DesktopScreen /> : <MobileScreen />}
```

Define `MobileScreen` and `DesktopScreen` in the same file (preferred for prototyping), or create `components/` and import from there. Your component should fill `100% × 100%` of its parent — the frame handles dimensions.

## Dev menu

The floating **⊞** button (top-left) controls:

| Control | What it does |
|---|---|
| **Platform** | Toggle Mobile / Desktop. Desktop fills the full window. |
| **Viewport** (mobile) | iPhone 17 Pro Max (440×956), Android Large (412×917), Responsive |
| **Scale** | Manual zoom. Auto-fits mobile frames to window height on load. |

Tap-and-hold the button to drag it anywhere. Double-tap to reset to origin.

## Key variables (available in `Home`)

```ts
isDesktop    // boolean — true when Platform = Desktop
isResponsive // boolean — true when Viewport = Responsive
platform     // "mobile" | "desktop"
preset       // active viewport preset key
scale        // current zoom (0.3–1.0)
w, h         // viewport dimensions in px (null when desktop/responsive)
```

## Design tokens

Brand values live in one place — `styles/globals.css`:

```css
:root {
  --accent:      #558BF7;   /* primary accent — swap per prototype */
  --accent-soft: color-mix(in srgb, var(--accent) 22%, transparent);
  --shell-bg:    #E8E8ED;   /* area around the device frame */
  --frame-bg:    #FFFFFF;   /* your screen's canvas */
}
```

`pages/index.tsx` consumes them as `var(--…)` strings (`ACCENT`, `ACCENT_SOFT`, `SHELL_BG`, `FRAME_BG`) and hardcodes no colors. The motion system's checkbox and progress colors are wired to `--accent` too.

Motion values are mirrored in JS where inline styles need them:

```ts
const MS = {
  dFast: "150ms",
  eOut:  "cubic-bezier(0.25, 0.46, 0.45, 0.94)",
} as const;
```

The full motion token set lives in `styles/motion-system.css` as CSS custom properties.

## Motion classes

Apply directly to elements, all prefixed `ms-`:

| Class | Effect |
|---|---|
| `ms-enter` | Fade-up entrance |
| `ms-enter-fade` | Fade-only entrance |
| `ms-btn` | Press scale feedback |
| `ms-btn-icon` | Tighter press scale (icons) |
| `ms-progress-linear` | Animated linear progress bar |
| `ms-progress-circular` | SVG circular progress |
| `ms-checkbox` | Animated checkbox (stroke-dashoffset) |
| `no-scrollbar` | Hides scrollbar (defined in `globals.css`) |

Use `data-stagger="N"` (0–7) on `ms-enter` elements for staggered entrances. A `prefers-reduced-motion` override neutralizes everything and must stay last in `motion-system.css`.

## Conventions

- **Single-file preferred** — keep all components in `pages/index.tsx` unless it exceeds ~1000 lines, then split into `components/<ScreenName>.tsx`
- **Inline styles for layout** — use Tailwind for typography utilities, inline styles for positioning and custom values
- **No data fetching** — mock data as local constants, or create `data/mock.ts` if a screen needs more than a handful
- **No routing** — simulate navigation with local state
- **Colors via tokens** — reach for `var(--accent)` rather than a new hex, so forks stay one-swap rebrandable
- **TypeScript strict** — all props typed, no `any` unless unavoidable

## Lint / type-check

```bash
npx tsc --noEmit
```

```bash
npx eslint .
```

Both should return clean before committing.

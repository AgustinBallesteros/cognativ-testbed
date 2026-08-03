# cognativ-testbed — Screen Testbed

## What this is
A whitelabel Next.js 16 prototype testbed for building and previewing app screens. It provides a pixel-accurate viewport shell with a floating dev menu — no routing, no backend, no state management overhead. You drop a screen component in, it renders inside the correct device frame.

It carries no project-specific content by design: fork it per prototype, swap `--accent` in `styles/globals.css`, and start building.

## Stack
- Next.js 16.2.1 (Pages Router, Turbopack)
- TypeScript 5
- React 19
- Tailwind CSS 4
- Inter font (via `next/font/google`)

## Running locally
```bash
npm install
npm run dev   # → http://localhost:3000
```

## File structure
```
pages/
  index.tsx        ← the only file you'll ever touch to add a screen
  _app.tsx         ← imports globals.css, do not modify
  _document.tsx    ← Next.js document + favicon link, do not modify
styles/
  globals.css      ← brand tokens + Tailwind + motion-system.css + overrides
  motion-system.css ← generic motion token layer (ms- classes + CSS vars)
public/
  favicon.svg      ← neutral placeholder mark, swap per prototype
```
Not present until you need them: `components/` (screens past ~1000 lines) and `data/mock.ts` (fixtures too big to inline).

## How to add a screen

Open `pages/index.tsx`. Find the single placeholder comment:

```tsx
{/* ── Drop your screen component here ── */}
<span style={{ color: "#ccc", fontSize: 13, fontFamily: "var(--font-inter)" }}>
  {isDesktop ? "Desktop" : "Mobile"} — add your screen here
</span>
```

Replace it with your component, using `isDesktop` to branch:

```tsx
{isDesktop ? <DesktopScreen /> : <MobileScreen />}
```

Define `MobileScreen` and `DesktopScreen` in the same file (preferred for prototyping) or import from `components/`. Keep it simple — this is a testbed, not a production app.

## Dev menu (floating ⊞ button, top-left)
- **Platform** — toggles Mobile / Desktop. Resizes the viewport frame accordingly.
- **Viewport** (mobile only) — iPhone 17 Pro Max (440×956), Android Large (412×917), Responsive (fills window).
- **Scale** — manual zoom for mobile frames. Auto-fits on load; drag to fine-tune.
- Tap-and-hold the button to drag it anywhere. Double-tap to reset to origin.

## Key variables available in `Home` (pages/index.tsx)
| Variable | Type | What it is |
|---|---|---|
| `isDesktop` | `boolean` | true when Platform = Desktop |
| `isResponsive` | `boolean` | true when Viewport = Responsive |
| `platform` | `"mobile" \| "desktop"` | raw platform state |
| `preset` | `PresetKey` | active viewport preset key |
| `scale` | `number` | current zoom (0.3–1.0) |
| `w`, `h` | `number \| null` | viewport dimensions (null when desktop/responsive) |

## Design tokens

Colors live in `styles/globals.css` — one swap point per fork:
```css
:root {
  --accent:      #558BF7;   /* buttons, rings, active states */
  --accent-soft: color-mix(in srgb, var(--accent) 22%, transparent);
  --shell-bg:    #E8E8ED;   /* around the device frame */
  --frame-bg:    #FFFFFF;   /* your screen's canvas */
}
```
`--accent` also drives `--ms-check-stroke` and `--ms-progress-fill`, so the motion system rebrands with it.

`pages/index.tsx` consumes them as `var(--…)` strings and hardcodes no colors:
```ts
const ACCENT      = "var(--accent)";
const ACCENT_SOFT = "var(--accent-soft)";
const SHELL_BG    = "var(--shell-bg)";
const FRAME_BG    = "var(--frame-bg)";

const MS = {              // motion tokens (JS mirror of motion-system.css)
  dFast: "150ms",
  eOut:  "cubic-bezier(0.25, 0.46, 0.45, 0.94)",
} as const;
```

Extend `MS` as needed — the full token set lives in `styles/motion-system.css` as CSS custom properties (`--ms-duration-*`, `--ms-ease-*`, etc.).

## Motion system (styles/motion-system.css)
Apply CSS classes directly to elements. All prefixed `ms-` to avoid collisions:

| Class | Effect |
|---|---|
| `ms-enter` | fade-up entrance (opacity + translateY) |
| `ms-enter-fade` | fade-only entrance |
| `ms-btn` | press scale feedback |
| `ms-btn-icon` | tighter press scale (icons/compact) |
| `ms-progress-linear` | animated linear progress bar |
| `ms-progress-circular` | SVG circular progress |
| `ms-checkbox` | animated checkbox (stroke-dashoffset) |
| `no-scrollbar` | hides scrollbar (globals.css) |

Use `data-stagger="N"` (0–7) on `ms-enter` elements for staggered entrance animations.

## Viewport frame internals
The frame div uses these styles — match them in your screen component:
```ts
{
  width:  isDesktop ? "100%" : w!,   // e.g. 440px for iPhone 17
  height: isDesktop ? "100%" : h!,   // e.g. 956px
  overflow: "hidden",
  background: FRAME_BG,              // var(--frame-bg), #fff by default
  userSelect: "none",
}
```
Your screen component receives no explicit width/height props — it should fill `100% × 100%` of its parent.

## Conventions
- **Single-file preferred**: keep all components for a screen in `pages/index.tsx` unless the file exceeds ~1000 lines, then split into `components/<ScreenName>.tsx`.
- **Inline styles over Tailwind for layout**: use Tailwind for typography utilities (`font-bold`, `text-black`, etc.) and inline styles for positioning, sizing, and custom values.
- **No data fetching**: mock data as local constants, or create `data/mock.ts` if a screen needs more than a handful.
- **No routing**: this is a single-page testbed — simulate navigation with local state.
- **Colors via tokens**: reach for `var(--accent)` rather than a new hex, so forks stay one-swap rebrandable.
- **TypeScript strict**: all props typed, no `any` unless unavoidable.

## Lint / type-check
```bash
npx tsc --noEmit
```
```bash
npx eslint .
```
Both should return clean (0 errors, 0 warnings) before committing.

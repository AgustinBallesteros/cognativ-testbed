---
description: Interview the user about a new prototype, then rebrand this testbed fork for it
---

# Set up a new prototype from the testbed

This repo is a whitelabel testbed. Your job is to turn this fork into a project-specific
prototype: ask for the details you need, apply them everywhere, verify, and report.

Do the intake **first**. Do not start editing files or guess values from the directory name.

**If the user already supplied some answers** — in the command arguments or anywhere earlier in the
conversation — treat those as given. Ask only for what's still missing, and skip the questions
entirely if you have all four essentials. Never re-ask something they've already told you.

## Step 1 — Intake

Use the **AskUserQuestion** tool. Ask the four essentials in one call:

1. **Project name** — kebab-case for `package.json`, plus the display name for docs.
   Offer the current directory name as the first option; "Other" lets them type it.
2. **What it is** — one line, used verbatim in `README.md` and `ONBOARDING.md`.
   Offer plausible shapes (mobile app screen, dashboard, marketing page, design-system demo)
   so they can pick and refine.
3. **Platform focus** — `Mobile only` / `Desktop only` / `Both`.
4. **Accent color** — the brand hex. Offer the current `#558BF7`, a "match an existing brand,
   I'll paste the hex" option, and "no preference, keep the default".

Then, only if the answers make them relevant, ask a second round (max 4):

- **Device presets** — keep iPhone 17 Pro Max (440×956) + Android Large (412×917), or swap for
  other dimensions. Ask only when platform focus includes Mobile and they hinted at a specific device.
- **Canvas colors** — `--shell-bg` / `--frame-bg`. Ask only for a non-white canvas (dark-mode app etc.).
- **First screens** — names of the screens to scaffold as stubs. Ask only when they described a
  multi-screen flow.

Keep it to one round when the project is simple. Never ask more than two rounds — anything else
can be adjusted later by hand.

## Step 2 — Apply

Work through all of these. Nothing here is optional except where marked.

| Target | Change |
|---|---|
| `package.json` | `"name"` → kebab-case project name |
| `styles/globals.css` | `--accent` → their hex. `--shell-bg` / `--frame-bg` only if they asked for a non-white canvas |
| `public/favicon.svg` | Outer `<rect>` `fill` → the accent hex (keep the white glyph strokes) |
| `README.md` | Replace the H1 and intro paragraph with the project name + their one-liner. **Delete the entire "Forking for a new prototype" section** and replace it with a plain `## Getting started` (`npm install` → `npm run dev`). Keep the Stack, Adding a screen, Dev menu, Key variables, Design tokens, Motion classes, Conventions, and Lint sections — they still describe how this repo works. Update the Design tokens block to show their actual hex |
| `ONBOARDING.md` | H1 and the "What this is" paragraph → project name + one-liner. Delete the "It carries no project-specific content by design" line — it isn't a template any more. Update the Design tokens block to their hex |
| `pages/index.tsx` | Replace the placeholder `<span>` with stubs named from their answers (e.g. `<MobileScreen />` / `<DesktopScreen />`, defined in the same file, each filling `100% × 100%`). Update `PRESETS` if they gave custom device dimensions. If they chose Desktop only, set the initial `platform` state to `"desktop"`; if Mobile only, leave it as `"mobile"` |
| `.claude/TEMPLATE` | **Delete it.** This is what stops the intake from re-running |
| `CLAUDE.md` | Remove the "First run" section (everything from `## First run` to the end of that block), leaving the `@AGENTS.md` import |
| `.claude/commands/new-prototype.md` | Delete this command file — it has done its job |

Do **not** touch: `styles/motion-system.css` (it is deliberately project-neutral), `AGENTS.md`,
`pages/_app.tsx`, `eslint.config.mjs`, `next.config.ts`, `tsconfig.json`, `postcss.config.mjs`.

Leave no hardcoded brand hex in `pages/index.tsx` — it consumes `var(--accent)` and friends
through the `ACCENT` / `ACCENT_SOFT` / `SHELL_BG` / `FRAME_BG` constants. Keep it that way.

## Step 3 — Check the git remote

Run `git remote -v`. If `origin` still points at a URL containing `cognativ-testbed`, this fork
was cloned rather than created from the template, so pushing would write to the testbed itself.
Tell the user, and offer to either re-point it:

```bash
git remote set-url origin <their new repo URL>
```

or detach entirely (`rm -rf .git && git init`) if they want a fresh history. **Do not run either
without their confirmation** — and never push to a `cognativ-testbed` remote from a fork.

## Step 4 — Verify

```bash
npx tsc --noEmit
```

```bash
npx eslint .
```

Both must be clean. If you deleted a page or API route, clear `.next` first — stale generated
route types in `.next/dev/types` will fail the type check.

## Step 5 — Report

Tell the user, briefly:

- the values you applied (name, one-liner, accent, presets)
- which files changed
- that `npm run dev` is ready, and where to add their screen (`pages/index.tsx`)
- the remote situation from Step 3, if it needs their attention

Do not commit unless they ask.

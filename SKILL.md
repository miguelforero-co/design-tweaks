---
name: design-tweaks
version: 1.1.0
description: |
  Universal visual tweaker. Opens an interactive dashboard-style playground where
  you can adjust any visual parameter in real time — design tokens, iconography,
  animation curves, spacing, sound parameters, color palettes, or any set of
  knobs that belongs to the current project — then export as JSON or a
  natural-language prompt to apply back to the codebase.
license: MIT
compatibility: claude-code
allowed-tools:
  - Bash
  - Read
  - Write
  - Edit
  - Grep
  - Glob
---

# design tweaks — Universal Visual Tweaker

A self-contained visual playground with a consistent dashboard shell: sidebar nav, controls panel, live preview. What goes inside changes based on what you're building. No build step, no dependencies.

## Core idea

The shell is always the same:
- Left sidebar: category nav (icon + label, sentence case)
- Center panel: controls for the selected category (sliders, pills, swatches, toggles)
- Right panel: live preview of the thing being tweaked

The content varies by project. Design tokens, icon styles, animation curves, sound envelopes, component options — anything with visual knobs belongs here.

## When this skill is invoked

Use this skill when the user:
- Runs `/design-tweaks`
- Says "open design tweaks", "let's tweak this visually", "open the tweaker"
- Is working on visual parameters that would benefit from live adjustment
- Pastes back a JSON block or "Copy Prompt" output from the explorer (apply mode)

---

## Mode 1 — Open the explorer

Open the HTML file in the user's default browser:

```bash
open ~/.claude/skills/design-tweaks/dev/explorer.html
```

The default explorer shows design system token categories. If you're working on something else (animation, iconography, etc.), say so and Claude will generate a custom explorer for that context.

Then tell the user:
> Explorer is open. Adjust any parameter in the sidebar. When done, hit **Copy JSON** or **Copy prompt** and paste it back here.

Wait for the user to paste back the exported config.

---

## Mode 2 — Apply exported config to the codebase

Triggered when the user pastes a JSON block or natural-language config from the explorer.

### Step 1 — Parse

Extract all key-value pairs. Preserve the exact format (don't convert units or rename keys).

### Step 2 — Find where these values live

Search the project in this order:
1. CSS custom properties — `grep -r "--" --include="*.css" -l`
2. JS/TS theme objects — `grep -r "fontBase\|radiusBase\|colorAccent\|theme\s*=" --include="*.ts" --include="*.js" --include="*.tsx" -l`
3. Tailwind config — `glob **/tailwind.config.*`
4. Token JSON — `glob **/tokens.json **/design-tokens.json`

If nothing found, ask the user before writing anything.

### Step 3 — Write values

Match keys to the project's naming convention. Don't convert formats or rename tokens.

### Step 4 — Confirm

List every file touched and every value updated.

---

## Mode 3 — Generate a custom tweaker for a specific subject

If the user is working on something other than design tokens (iconography, motion curves, sound, component options, etc.), generate a custom version of the explorer HTML in `~/.claude/skills/design-tweaks/dev/` with:
- Same shell design (topbar, sidebar, controls, preview)
- Categories and controls matched to the subject
- Same Copy JSON / Copy Prompt export
- Same dark mode toggle

Examples of custom tweaker subjects:
| Subject | Categories | Controls |
|---|---|---|
| Icon style | Stroke, Corner, Size, Optical sizing | Weight slider, corner radius, size grid |
| Animation | Timing, Easing, Stagger, Spring | Duration sliders, bezier picker, spring mass/stiffness |
| Sound design | Envelope, Tone, Reverb, Mix | ADSR sliders, frequency, waveform pills |
| Component options | Density, Shape, Color role, Interaction | Compact/default/comfortable pills, radius, accent |

**Shell invariants** — these never change regardless of subject:
- Font: Plus Jakarta Sans (loaded from Google Fonts)
- Background: `#e9e7e4` (warm gray)
- Sidebar/controls/topbar: white `#fff`, border `#e5e3df`
- Preview area bg: `#f2f0ed`
- Labels: sentence case, 13px, 500 weight
- No uppercase eyebrows, no gradient text, no glow effects
- Monospace font only for values/numbers

---

## Modular type scale

```
xs  = fontBase × ratio^-3
sm  = fontBase × ratio^-1
base = fontBase
lg  = fontBase × ratio^1
xl  = fontBase × ratio^2
2xl = fontBase × ratio^3
3xl = fontBase × ratio^4
4xl = fontBase × ratio^5
```

Round to 1 decimal (e.g. `12.4px`).

---

## Shadow string reconstruction

```
none → none
xs   → 0 1px 2px rgba(0,0,0,{opacity})
sm   → 0 1px 3px rgba(0,0,0,{o}), 0 1px 2px -1px rgba(0,0,0,{o})
md   → 0 4px 6px -1px rgba(0,0,0,{o}), 0 2px 4px -2px rgba(0,0,0,{o})
lg   → 0 10px 15px -3px rgba(0,0,0,{o}), 0 4px 6px -4px rgba(0,0,0,{o})
xl   → 0 20px 25px -5px rgba(0,0,0,{o}), 0 8px 10px -6px rgba(0,0,0,{o})
2xl  → 0 25px 50px -12px rgba(0,0,0,{o})
```

---

## Easing presets → cubic-bezier

| Preset | cubic-bezier |
|---|---|
| linear | 0, 0, 1, 1 |
| standard | 0.2, 0, 0, 1 |
| decelerate | 0, 0, 0, 1 |
| accelerate | 0.3, 0, 1, 1 |
| emphasized | 0.05, 0.7, 0.1, 1 |
| bounce | 0.34, 1.56, 0.64, 1 |
| smooth | 0.4, 0, 0.2, 1 |

---

## Rules

- Never open the explorer more than once per invocation unless asked.
- Never guess where tokens live — search first, confirm if ambiguous.
- Never convert the project's token format (CSS → JS, etc.) unless asked.
- Never apply dark-mode token values to light-mode variables or vice versa.
- If the pasted config has `"mode": "dark"`, apply to dark-mode overrides only.
- When generating a custom tweaker, always use the same shell design — no exceptions.

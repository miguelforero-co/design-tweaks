---
name: design-tweaks
version: 1.0.0
description: |
  Interactive design system token explorer. Opens a visual playground where
  you can tweak colors, typography, spacing, border radius, shadows, motion,
  opacity, blur, and grid tokens in real time — then export as JSON or a
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

# design tweaks — Design System Token Explorer

A self-contained visual playground for tuning design tokens. No build step, no dependencies, runs entirely in the browser.

## When this skill is invoked

Use this skill when the user:
- Runs `/design-tweaks`
- Asks to "open design tweaks", "tweak tokens", "open the design system explorer"
- Pastes back a JSON block or "Copy Prompt" output from the explorer (apply mode)

---

## Mode 1 — Open the explorer

Open the HTML file in the user's default browser:

```bash
open ~/.claude/skills/design-tweaks/dev/explorer.html
```

Then tell the user:
> Explorer is open. Adjust any token in the sidebar — color, type, spacing, radius, shadow, motion, opacity, blur, or grid. When you're happy, hit **Copy JSON** or **Copy Prompt** and paste it back here to apply to the codebase.

Do not do anything else. Wait for the user to paste back the exported config.

---

## Mode 2 — Apply exported tokens to the codebase

Triggered when the user pastes a JSON block (from Copy JSON) or a natural-language block (from Copy Prompt).

### Step 1 — Parse the config

**JSON format:**
```json
{
  "color": { "background": "#e8e8e8", "surface": "#ffffff", "accent": "#18181b", ... },
  "typography": { "fontBase": "15.5px", "scaleRatio": 1.25, "fontFamily": "system", ... },
  "spacing": { "unit": "4px" },
  "border": { "radius": "11px", "width": "1px", "style": "solid" },
  "shadow": { "level": "sm", "opacity": 0.1 },
  "motion": { "fast": "100ms", "base": "200ms", "slow": "350ms", "easing": "standard" },
  "opacity": { "disabled": 0.38, "overlay": 0.6, "muted": 0.5 },
  "effects": { "blurSm": "4px", "blurMd": "8px", "blurLg": "16px", "backdropBlur": "8px" },
  "grid": { "columns": 12, "gutter": "24px", "margin": "32px" }
}
```

**Prompt format** — parse key-value pairs from the natural-language block.

### Step 2 — Locate design token files

Search the project for where tokens are currently defined. Check in this order:

1. **CSS custom properties** — `grep -r "\-\-.*color\|--font\|--radius\|--spacing" --include="*.css" -l`
2. **JS/TS theme objects** — `grep -r "fontBase\|radiusBase\|colorAccent\|designTokens\|theme\s*=" --include="*.ts" --include="*.js" --include="*.tsx" -l`
3. **Tailwind config** — `glob **/tailwind.config.*`
4. **Design token JSON** — `glob **/tokens.json **/design-tokens.json **/*.tokens.json`

If no token files are found, ask the user where tokens are defined before writing anything.

### Step 3 — Write the values

Map the exported tokens to the project's naming convention and update the files. Preserve the project's existing format (CSS vars, JS object, JSON, etc.) — don't convert between formats.

**CSS custom property mapping:**
| Explorer key | CSS token name (typical) |
|---|---|
| color.background | `--color-bg`, `--background`, `--dt-bg` |
| color.surface | `--color-surface`, `--surface` |
| color.accent | `--color-accent`, `--accent`, `--primary` |
| typography.fontBase | `--font-size-base`, `--text-base` |
| typography.scaleRatio | (computed scale — update individual size steps) |
| spacing.unit | `--space-unit`, `--spacing-base` |
| border.radius | `--radius`, `--border-radius-base` |
| shadow.level / opacity | (recompute shadow strings from level + opacity) |
| motion.fast / base / slow | `--duration-fast`, `--duration-base`, `--duration-slow` |
| motion.easing | `--ease`, `--easing-standard` |

If the project has its own naming convention, match it — don't rename existing tokens.

### Step 4 — Confirm

After writing, list every file touched and every token updated. Do not open the explorer again unless the user asks.

---

## Modular type scale

The explorer computes the full type scale from `fontBase` × `scaleRatio^step`. When applying to a project that uses a computed scale, update only the base and ratio. When the project hardcodes each step, compute and write all sizes:

```
xs  = fontBase × ratio^-2
sm  = fontBase × ratio^-1
base = fontBase
lg  = fontBase × ratio^1
xl  = fontBase × ratio^2
2xl = fontBase × ratio^3
3xl = fontBase × ratio^4
4xl = fontBase × ratio^5
```

Round to 1 decimal place (e.g. `12.4px`).

---

## Shadow string reconstruction

The explorer stores `shadowLevel` and `shadowOpacity`. If the project stores full shadow strings, reconstruct them:

```
none → none
xs   → 0 1px 2px 0 rgba(0,0,0,{opacity})
sm   → 0 1px 3px 0 rgba(0,0,0,{opacity}), 0 1px 2px -1px rgba(0,0,0,{opacity})
md   → 0 4px 6px -1px rgba(0,0,0,{opacity}), 0 2px 4px -2px rgba(0,0,0,{opacity})
lg   → 0 10px 15px -3px rgba(0,0,0,{opacity}), 0 4px 6px -4px rgba(0,0,0,{opacity})
xl   → 0 20px 25px -5px rgba(0,0,0,{opacity}), 0 8px 10px -6px rgba(0,0,0,{opacity})
2xl  → 0 25px 50px -12px rgba(0,0,0,{opacity})
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
- Never guess where tokens live — search first, confirm before writing if ambiguous.
- Never convert the project's token format (CSS → JS, etc.) unless explicitly asked.
- Never apply dark-mode token values to light-mode variables or vice versa.
- If the pasted config has `"mode": "dark"`, apply to dark-mode token overrides only.

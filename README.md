# design tweaks

A Claude Code skill that opens an interactive design system token explorer — tweak every token visually, then export as JSON or a prompt to apply back to your codebase.

![design tweaks explorer](dev/explorer.html)

## What it does

- **Visual playground** — adjust color, typography, spacing, border, shadow, motion, opacity, blur, and grid tokens in real time
- **Live preview** — every change instantly updates sample components (cards, buttons, inputs, type scale, color palette, elevation, motion bars)
- **Export** — Copy JSON (structured token object) or Copy Prompt (natural-language description) to feed back to Claude
- **Apply** — paste the exported config into Claude and it writes the tokens directly to your project files

## Installation

```bash
mkdir -p ~/.claude/skills
git clone https://github.com/emeforero/design-tweaks.git ~/.claude/skills/design-tweaks
```

## Usage

In any Claude Code session:

```
/design-tweaks
```

The explorer opens in your browser. Adjust tokens, hit **Copy JSON** or **Copy Prompt**, paste back into Claude — done.

## Token categories

| Category | What you control |
|---|---|
| Color | Background, surface, border, text, muted, faint + semantic (accent, success, warning, error, info) |
| Typography | Base size, modular scale ratio, font family, weight, line height, letter spacing |
| Spacing | Base unit (generates full 4pt/8pt scale) |
| Border | Radius (base + computed scale), width, style |
| Shadow | Elevation level (none → 2xl), opacity |
| Motion | Fast / base / slow durations, easing curve picker with bezier visualizer |
| Opacity | Disabled, overlay/scrim, muted/placeholder |
| Effects | Filter blur (sm/md/lg), backdrop blur |
| Grid | Columns, gutter, margin |

## Export formats

**Copy JSON** — structured object, apply with `/design-tweaks`:
```json
{
  "color": { "background": "#e8e8e8", "accent": "#18181b", ... },
  "typography": { "fontBase": "15.5px", "scaleRatio": 1.25, ... },
  ...
}
```

**Copy Prompt** — paste directly into any Claude conversation:
```
Design system config (mode: light):

Color: bg #e8e8e8 · surface #ffffff · accent #18181b
Typography: 15.5px base · 1.25× scale · system
...
```

## Requirements

- Claude Code
- macOS (uses `open` to launch the browser)
- No build step, no dependencies — pure HTML/CSS/JS

## License

MIT

# design tweaks

A Claude Code skill — a universal visual tweaker that opens an interactive dashboard-style playground, adapts its controls to whatever you're building, and exports your decisions as JSON or a natural-language prompt to apply back to the codebase.

## What it does

1. **Scans your project** — reads the codebase to understand what visual systems exist before asking anything
2. **Asks what to tweak** — proposes relevant options based on what it found (design tokens, animation, iconography, component options, sound, etc.)
3. **Opens the right explorer** — either the default design tokens playground or a generated custom tweaker for your subject
4. **Live preview** — every change instantly updates the preview on the right
5. **Exports** — Copy JSON or Copy Prompt, paste back into Claude, it writes the values to your files

The shell (dashboard layout, font, colors) is always the same. What's inside changes.

## Installation

```bash
mkdir -p ~/.claude/skills
git clone https://github.com/miguelforero-co/design-tweaks.git ~/.claude/skills/design-tweaks
```

## Usage

In any Claude Code session:

```
/design-tweaks
```

Claude scans the project, asks what you want to tweak, opens the right playground. Adjust parameters, hit **Copy JSON** or **Copy prompt**, paste back into Claude.

## Subjects

| Subject | Categories | Preview |
|---|---|---|
| Design tokens | Color, Typography, Spacing, Border, Shadow, Motion, Opacity, Effects, Grid | Component card, type scale, palette, shadows, radii, motion bars |
| Iconography | Stroke, Corner, Size, Optical sizing, Color | Live SVG icon grid |
| Animation | Timing, Easing, Spring physics, Stagger | Animated elements that play on click |
| Sound design | Envelope, Tone, Reverb, Mix | ADSR curve + Web Audio playback |
| Component options | Density, Shape, Color role, Interaction | Live component render |

Custom subjects are generated automatically — same shell, different controls.

## Export formats

**Copy JSON:**
```json
{
  "mode": "light",
  "color": { "bg": "#f0f0ee", "accent": "#18181b", ... },
  "typography": { "fontBase": "15px", "scaleRatio": 1.25, ... }
}
```

**Copy prompt:**
```
Design system config (mode: light):

Color: bg #f0f0ee · surface #ffffff · accent #18181b
Typography: 15px base · 1.25× scale · system family · weight 400
...
```

## Requirements

- Claude Code
- macOS (uses `open` to launch the browser)
- Internet access for the first load (Google Fonts)
- No build step, no dependencies — pure HTML/CSS/JS

## License

MIT

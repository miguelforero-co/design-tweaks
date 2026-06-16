# design tweaks

A Claude Code skill that opens an interactive visual tweaker — a dashboard-style playground where you adjust parameters live, see them reflected in a preview instantly, and export exact values as JSON or a structured prompt to apply back to the codebase.

![design tweaks explorer](preview.png)

---

## What it is

Not a design token editor. Not a theme picker. A general-purpose visual tweaker for **any parameter that benefits from live adjustment**.

Claude reads your conversation, understands what you're working on, asks what it needs to know, then opens the right playground — or generates one from scratch if the subject is something new.

**Works for:**
- Design tokens — colors, typography, spacing, border, shadow, motion, opacity, blur, grid
- Animation — timing curves, spring physics, stagger, duration
- Iconography — stroke weight, corner radius, optical sizing, color
- Component options — density, shape, color roles, interaction states
- Sound — envelope, tone, reverb, mix levels
- Anything else with visual or numeric knobs

The shell (layout, font, colors) is always the same. What's inside adapts.

---

## How it works

```
/design-tweaks
```

1. Claude reads what you've been building in the conversation
2. Asks clarifying questions until it knows exactly what to tweak
3. Opens the right explorer in your browser
4. You adjust — the preview updates live
5. Hit **Copy JSON** or **Copy prompt** — paste back into Claude
6. Claude writes the exact values to your project files

---

## Export format

Both exports contain **every parameter with exact values** — no rounding, no omissions.

**Copy JSON:**
```json
{
  "mode": "light",
  "color": {
    "bg": "#f0f0ee",
    "surface": "#ffffff",
    "border": "#e2e0dc",
    "text": "#1a1917",
    "muted": "#78766e",
    "accent": "#18181b",
    "success": "#166534",
    "warning": "#92400e",
    "error": "#991b1b",
    "info": "#1e40af"
  },
  "typography": {
    "fontBase": "15px",
    "scaleRatio": 1.25,
    "fontFamily": "system",
    "fontWeight": 400,
    "lineHeight": 1.6,
    "letterSpacing": "0em"
  },
  "spacing": { "unit": "4px" },
  "border": { "radius": "10px", "width": "1px", "style": "solid" },
  "shadow": { "level": "sm", "opacity": 0.1 },
  "motion": { "fast": "100ms", "base": "200ms", "slow": "350ms", "easing": "standard" }
}
```

**Copy prompt** — same values, structured as a natural-language instruction to paste into any Claude conversation.

---

## Installation

```bash
mkdir -p ~/.claude/skills
git clone https://github.com/miguelforero-co/design-tweaks.git ~/.claude/skills/design-tweaks
```

---

## Updating

Pull the latest version at any time:

```bash
cd ~/.claude/skills/design-tweaks && git pull
```

**What gets updated:** `explorer.html` (the default token tweaker) and `SKILL.md` (the skill instructions).

**What stays untouched:** Any custom tweakers Claude generated for your specific projects live only on your machine (they're in `dev/` but gitignored). A `git pull` never touches them.

**If you modified skill files manually:** That's a merge conflict — don't edit skill files directly. Claude uses them as-is.

---

## Requirements

- Claude Code
- macOS (uses `open` to launch the browser)
- Internet access on first load (Google Fonts — Plus Jakarta Sans)
- No build step, no dependencies — pure HTML/CSS/JS

---

## License

MIT — Miguel Forero, 2026

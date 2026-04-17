# Slides Skill

An AI coding skill that generates interactive HTML slide presentations from any template or design system.

## Features

- **1920×1080 pixel-perfect slides** that scale to any viewport
- **Click-to-reveal sub-steps** — advance through content within a slide before moving to the next
- **Timed animations** — elements appear automatically with configurable delays
- **Animated counters** — numbers count up with easing for impact
- **Embedded media** — videos in device frames, screenshots, inline SVG diagrams
- **Speaker notes** — full presenter mode with timer, synced across tabs
- **Architecture diagrams** — animated SVG diagrams with sequential component reveals
- **QR code generation** — auto-generate QR codes for URLs
- **PPTX export** — convert to editable PowerPoint on request
- **Template-agnostic** — works with any brand/design system, or use the included default

## Install

### Kiro

```bash
git clone https://github.com/kandmgawron/presentations.git ~/.kiro/skills/slides
```

The skill activates automatically when you ask Kiro to create or edit a presentation.

### Claude Code (claude-code / claude CLI)

Add the `SKILL.md` as a CLAUDE.md file in your project, or reference it in your prompt:

```bash
# Option 1: Copy into your project
git clone https://github.com/kandmgawron/presentations.git /tmp/slides-skill
cp /tmp/slides-skill/SKILL.md ./CLAUDE.md
cp /tmp/slides-skill/html-template.md ./html-template.md
cp -r /tmp/slides-skill/assets ./assets

# Option 2: Reference directly in your prompt
claude "Read /tmp/slides-skill/SKILL.md and create a presentation about [topic]"
```

### GitHub Copilot

Add the skill files to your project's `.github/copilot-instructions.md` or reference them as context:

```bash
# Copy into your project
git clone https://github.com/kandmgawron/presentations.git /tmp/slides-skill
cp /tmp/slides-skill/SKILL.md ./.github/copilot-instructions.md
cp /tmp/slides-skill/html-template.md ./html-template.md
cp -r /tmp/slides-skill/assets ./assets
```

Or in Copilot Chat, attach the `SKILL.md` and `html-template.md` files as context and ask it to generate slides.

### Any AI Coding Tool

The skill is just markdown files — it works with any AI tool that accepts instructions:

1. Clone this repo
2. Feed `SKILL.md` as system instructions or context
3. Feed `html-template.md` as the template reference
4. Ask it to generate a presentation

The `SKILL.md` contains all the patterns, conventions, and code examples the AI needs.

## Usage

In Kiro, just ask:

- *"Create a presentation about [topic]"*
- *"Make a slide deck on [topic]"*
- *"Improve my presentation at [path]"*
- *"Add animations to slide 5"*
- *"Export to PPTX"*

### Custom Templates

Provide your own template or design doc and the skill will match your brand:

- *"Create a presentation using the template at ./my-template.html"*
- *"Edit the presentation at ./talks/my-talk/index.html"*

### Presenter Mode

1. Open the HTML file in your browser
2. Press **S** to toggle speaker notes
3. Open in two tabs — share one with the audience, use the other for notes
4. Navigation syncs automatically between tabs

### PDF Export

Open in browser → **Cmd+P** (or Ctrl+P) → Print to PDF. Check "Background graphics" and select Landscape.

## What's Included

| File | Purpose |
|------|---------|
| `SKILL.md` | Skill definition with triggers, instructions, and patterns |
| `html-template.md` | Default neutral HTML/CSS/JS template |
| `pptx-export.md` | PPTX export guide with coordinate mapping |
| `assets/` | Placeholder background images |

## Customisation

The default template uses CSS variables for easy theming:

```css
:root {
  --bg-dark: #1a1a2e;      /* Slide background */
  --accent: #e94560;        /* Accent color */
  --text-white: #FFFFFF;    /* Primary text */
  --text-gray: rgba(255, 255, 255, 0.7); /* Secondary text */
}
```

Change these variables to match your brand. Replace the assets in `assets/` with your own logos and backgrounds.

## License

MIT

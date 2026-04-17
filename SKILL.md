---
name: slides
description: Generate interactive HTML slide presentations from any template or design system. Supports click-to-reveal, animations, embedded media, speaker notes, and PPTX export. Use when the user asks to create, edit, or improve a presentation.
---

# Slides Skill

Generate pixel-perfect, interactive HTML slide presentations. Works with any brand template or design system. Includes a clean default template, or bring your own.

## Trigger
Use this skill when the user asks to:
- "Create a presentation about [Topic]"
- "Make a slide deck on [Topic]"
- "Improve / edit / animate my presentation"
- "Export to PPTX / PowerPoint / Google Slides"

## Templates & Branding

### Default Template
If no template is specified, use the neutral template from `html-template.md` with placeholder assets from this skill's `assets/` directory. The user can customise colors, fonts, and logos after generation.

### Custom Templates
If the user provides a template file, design doc, or existing presentation:
1. **Read the template first** — extract CSS variables, class names, fonts, colors, and layout patterns.
2. **Match the existing design system** — don't introduce new CSS frameworks or class names.
3. **Preserve the JS engine** — every presentation has its own slide navigation JS. Read it, understand it, extend it.
4. **Adapt, don't override** — match the convention already in use.

## Instructions

### 1. Understand the Request
- The user provides a topic, raw content, or an existing presentation to modify.
- Intelligently extrapolate a structured narrative — no questionnaire needed.
- For new presentations: generate HTML slides by default. PPTX export only when explicitly requested.
- For existing presentations: read the full file before making changes. Match the existing style exactly.

### 2. Presentation Structure
- **Title Slide** — always first. Topic, speaker name, role.
- **Speaker Intro** — optional. Photo, bio, QR code for contact links.
- **Agenda Slide** — usually second. Numbered sections.
- **Section Dividers** — between major topics. Big number + section title.
- **Content Slides** — mix layouts: bullet lists, 2-column comparisons, 3-column cards, icon grids, flow diagrams, code blocks, architecture diagrams, metric highlights.
- **Closing Slide** — thank you, contact info, QR code.

### 3. Interactive Features

#### Click-to-Reveal (Sub-Steps)
Add `data-step="N"` to any element to hide it until the Nth click on that slide. The JS `next()` method checks for unrevealed steps before advancing.

```html
<div class="card" data-step="1">First point</div>
<div class="card" data-step="2">Second point</div>
<div class="callout" data-step="3">Summary</div>
```

CSS:
```css
[data-step] { opacity:0; transform:translateY(20px); transition:opacity 0.5s ease, transform 0.5s ease; }
[data-step].step-visible { opacity:1; transform:translateY(0); }
```

JS (add to the deck's `next()` method):
```javascript
next() {
  var slide = this.slides[this.cur];
  var hidden = slide.querySelectorAll('[data-step]:not(.step-visible)');
  if (hidden.length > 0) {
    var steps = Array.from(hidden).map(el => parseInt(el.dataset.step));
    var nextStep = Math.min(...steps);
    slide.querySelectorAll('[data-step="' + nextStep + '"]').forEach(el => el.classList.add('step-visible'));
    return;
  }
  this.go(this.cur + 1);
}
```

Reset steps when navigating away from a slide.

#### Timed Animations
Use CSS `animation-delay` scoped to `#slideId.active .element` so animations only trigger when the slide is active.

#### Animated Counters
Use `requestAnimationFrame` with an ease-out curve. Trigger via MutationObserver watching for the slide's `active` class.

#### Embedded Media
- **Videos**: `<video autoplay muted loop playsinline>` inside a phone/device frame div.
- **Screenshots**: Wrap in a device-frame div with rounded corners and border.
- **QR Codes**: Generate with the `qrcode` Python library, save as PNG.
- **SVG Diagrams**: Inline SVGs with animated reveals using CSS classes.

### 4. Speaker Notes
Add `<div class="notes">` inside each slide div. Write thorough talking points, not just bullet summaries. The notes panel toggles with the `S` key and syncs across tabs via BroadcastChannel.

### 5. Architecture Diagrams
Build inline SVGs with:
- Colored boxes per component (match service brand colors where possible).
- Animated arrows using `stroke-dasharray` / `stroke-dashoffset`.
- Sequential reveal with CSS animation delays scoped to `.active`.
- Corresponding bullet list that reveals in sync.

### 6. Asset Management
- Every presentation needs an `assets/` directory alongside the HTML.
- For the default template: copy placeholder assets from this skill's `assets/` directory.
- For custom presentations: use whatever assets the user provides.
- Generate QR codes, resize images, and convert media as needed using Python.

### 7. Finalizing
After generating, tell the user about:
- **PPTX Export**: Available on request (see below).
- **PDF Export**: Open in browser → Print to PDF (Cmd+P), check "Background graphics", Landscape.
- **Presenter Mode**: Press `S` for speaker notes. Two tabs sync navigation automatically.
- **No-Notes Version**: Can generate a stripped version for sharing.

### 8. Editing Existing Presentations
When modifying an existing presentation:
- **Read the full HTML first** before making any changes.
- **For files outside the workspace**: use Python scripts to read/write (avoid shell truncation with large files).
- **Match the existing design system** — fonts, colors, class names, animation patterns.
- **Preserve the JS navigation engine** — extend it, don't replace it.
- **Verify writes landed** — especially for symlinked or external files.

## PPTX Export (only when explicitly requested)

1. Read `pptx-export.md` for instructions and coordinate mapping.
2. Read the actual HTML of the presentation.
3. Write a one-off Node.js script (using `pptxgenjs` and `cheerio`) tailored to the specific slides.
4. Run the script to produce the `.pptx` file.

## Supporting Files

- `html-template.md`: Neutral default HTML/CSS/JS template.
- `pptx-export.md`: PPTX export guide with coordinate system and API reference.
- `assets/`: Placeholder background images.

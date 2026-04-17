# PPTX Export Reference

Instructions for dynamically generating an editable `.pptx` file from a DoiT HTML presentation. Read this file when the user asks to export to PPTX, PowerPoint, or Google Slides.

**Key principle:** Do NOT use a static conversion script. Instead, read the actual HTML of the generated presentation and write a one-off Node.js script tailored to that specific slide content and layout. This ensures every layout—no matter how creative—is faithfully reproduced.

## Process

1. Check that Node.js, `pptxgenjs`, and `cheerio` are available. If not, run `npm install pptxgenjs cheerio` in the presentation directory.
2. Read the generated HTML file thoroughly. Understand every slide's structure, positioning, inline styles, text formatting, and layout.
3. Write a conversion script (`export.js`) in the presentation directory that recreates each slide in PPTX using the pptxgenjs API.
4. Run the script: `node export.js`
5. Confirm the `.pptx` was created and give the user the path.
6. Optionally clean up `export.js` afterward.

## Coordinate System

The HTML canvas is `1920×1080 px`. PPTX widescreen is `13.33″ × 7.5″`.

```
1 CSS-px  =  13.33 / 1920  =  0.006943 inches
1 CSS-px  =  0.006943 × 72 =  0.5 points (for font sizes)
```

Helper functions to include in every generated script:

```javascript
const W = 13.33, H = 7.5;
const inch = (px) => +(px * W / 1920).toFixed(3);
const pts  = (cssPx) => Math.round(cssPx * W / 1920 * 72);
```

**Critical:** Use `inch()` and `pts()` for ALL conversions. Never guess or use arbitrary values. Every CSS `top`, `left`, `width`, `height` must go through `inch()`. Every CSS `font-size` must go through `pts()`.

### Font Size Conversion Table

Always use `pts()` to convert. For reference, here are common CSS font sizes and their PPTX equivalents:

| CSS font-size | PPTX points | Typical usage |
|---|---|---|
| 150px | 75pt | Hero stat number |
| 80px | 40pt | Title slide text |
| 72px | 36pt | Slide heading (h1) |
| 64px | 32pt | Agenda numbers |
| 48px | 24pt | Large subheading |
| 42px | 21pt | Metric values |
| 40px | 20pt | Bullet items / agenda text |
| 36px | 18pt | Sub-bullet items |
| 32px | 16pt | Subtitle |
| 28px | 14pt | Body text / content area |
| 24px | 12pt | Metric descriptions |
| 20px | 10pt | Footer / small text |

**If the HTML uses a font-size not in this table, always calculate with `pts()` — do not round to the nearest table entry.**

## DoiT Brand Tokens

```javascript
const COLORS = {
  bg:    '000433',   // --bg-dark
  pink:  'FC3165',   // --accent-pink
  white: 'FFFFFF',
  gray:  'B3B4C2',   // rgba(255,255,255,0.7) pre-composited on bg
};
const FONT = 'Montserrat';
```

## pptxgenjs API Quick Reference

### Setup

```javascript
const PptxGenJS = require('pptxgenjs');
const pptx = new PptxGenJS();
pptx.defineLayout({ name: 'DOIT', width: 13.33, height: 7.5 });
pptx.layout = 'DOIT';
```

### Slide with background image

```javascript
const slide = pptx.addSlide();
slide.background = { path: 'assets/bg-title.png' };
// or solid color fallback:
slide.background = { color: '000433' };
```

- Title slides use `assets/bg-title.png`
- Content slides use `assets/bg-content.png`

### Text

```javascript
slide.addText('Hello', {
  x: 0.56,              // inches from left
  y: 0.67,              // inches from top
  w: 12.22,             // width in inches
  h: 0.6,               // height in inches
  fontSize: 36,          // points — always use pts() to convert from CSS px
  fontFace: 'Montserrat',
  bold: true,
  italic: false,
  color: 'FFFFFF',       // hex, no #
  align: 'left',         // 'left' | 'center' | 'right'
  valign: 'top',         // 'top' | 'middle' | 'bottom'
  wrap: true,
  lineSpacingMultiple: 1.4,  // maps to CSS line-height
  paraSpaceBefore: 0,         // points — space above paragraph
  paraSpaceAfter: 6,          // points — space below paragraph
  margin: [inch(20), inch(25), inch(20), inch(25)],  // [top, right, bottom, left] in inches — maps to CSS padding
});
```

### Line height and spacing

**Critical: Always convert CSS `line-height` to pptxgenjs `lineSpacingMultiple`.** If not set, PPTX defaults to ~1.0 which looks cramped compared to the HTML.

| CSS line-height | pptxgenjs option | Notes |
|---|---|---|
| `1.2` | `lineSpacingMultiple: 1.2` | Tight — headings |
| `1.4` | `lineSpacingMultiple: 1.4` | Default content area |
| `1.6` | `lineSpacingMultiple: 1.6` | Bullet lists / readable body |
| `1.8` | `lineSpacingMultiple: 1.8` | Spacious |

Read the CSS `line-height` from the HTML (either in `<style>` block or inline style) and apply it. If the HTML uses a unitless value like `1.6`, use `lineSpacingMultiple: 1.6` directly.

For `paraSpaceAfter` / `paraSpaceBefore`, convert CSS `margin-bottom` / `margin-top` on block elements: `margin_in_px * 0.5 = points`.

### Padding (inner margins on text boxes and cards)

**Critical: CSS `padding` on boxes must be converted to pptxgenjs `margin`.** The `margin` property on `addText` controls the internal padding of the text box, NOT outer spacing.

```javascript
// CSS: padding: 20px 25px;
// → margin: [top, right, bottom, left] in inches
margin: [inch(20), inch(25), inch(20), inch(25)]

// CSS: padding: 30px;
margin: inch(30)  // single value applies to all sides
```

When a card or box has CSS `padding`, always set `margin` on the `addText` call placed inside that box. Without this, text will sit flush against the box edges instead of having the intended inset.

### Mixed formatting (text runs) — for inline bold, color changes, etc.

This is critical for preserving `<strong>` tags, colored spans, and mixed formatting within a single text box.

```javascript
// Example: CSS font-size is 28px → pts(28) = 14
slide.addText([
  { text: 'Lower risk of heart attack ', options: { bold: true, color: 'FFFFFF', fontSize: pts(28) } },
  { text: 'among cat owners, according to a study.', options: { bold: false, color: 'B3B4C2', fontSize: pts(28) } },
], {
  x: inch(400), y: inch(180), w: inch(1100), h: inch(300),
  fontFace: 'Montserrat',
  wrap: true,
});
```

**Rules for text runs:**
- Each `<strong>` or `<b>` tag → a run with `bold: true`
- Colored text (inline `style="color:..."`) → extract hex and set `color`
- Each `<br>` or block boundary → insert `\n` in the text
- Pink-colored bold text in the HTML → `{ bold: true, color: 'FC3165' }`

### Bullet lists

```javascript
slide.addText([
  { text: 'First item',  options: { bullet: { code: '2022' }, indentLevel: 0, fontSize: pts(40) } },
  { text: 'Sub item',    options: { bullet: { code: '25CB' }, indentLevel: 1, fontSize: pts(36) } },
  { text: 'Second item', options: { bullet: { code: '2022' }, indentLevel: 0, fontSize: pts(40) } },
], {
  x: inch(80), y: inch(200), w: inch(1760), h: inch(600),
  fontFace: 'Montserrat',
  color: 'FFFFFF',
  valign: 'top',
  lineSpacingMultiple: 1.6,  // match CSS line-height on ul
  paraSpaceAfter: 4,
});
```

Bullet codes: `2022` = •, `25CB` = ○, `25BA` = ►

### Shapes (rectangles, lines, accent bars)

```javascript
// Filled rectangle (e.g., metric card background)
// Convert CSS padding to inch() for the text margin placed on top
slide.addShape(pptx.shapes.RECTANGLE, {
  x: 1.0, y: 2.0, w: 5.5, h: 1.2,
  fill: { color: 'FFFFFF', transparency: 95 },  // 0=opaque, 100=invisible
  rectRadius: 0.07,                              // CSS border-radius → inch()
});
// Then place addText on top with margin matching the CSS padding:
slide.addText('Card content', {
  x: 1.0, y: 2.0, w: 5.5, h: 1.2,
  margin: [inch(20), inch(25), inch(20), inch(25)],  // matches CSS padding: 20px 25px
  fontSize: pts(24), fontFace: 'Montserrat', color: 'FFFFFF',
  lineSpacingMultiple: 1.4,
  valign: 'top', wrap: true,
});

// Thin line / divider
slide.addShape(pptx.shapes.RECTANGLE, {
  x: inch(80), y: inch(500), w: inch(100), h: 0.02,
  fill: { color: 'FC3165' },
});

// Pink left accent bar on a card — convert CSS border-left width
slide.addShape(pptx.shapes.RECTANGLE, {
  x: 1.0, y: 2.0, w: inch(4), h: 1.2,  // CSS: border-left: 4px solid
  fill: { color: 'FC3165' },
});
```

### Images — NEVER stretch logos

**Critical: Always read the actual image dimensions and preserve the aspect ratio.** Never hardcode both `w` and `h` — specify ONE dimension from the CSS and compute the other from the image's real aspect ratio. Skewed logos are unacceptable.

Every generated script MUST include this helper:

```javascript
function pngSize(filePath) {
  const buf = Buffer.alloc(24);
  const fd = fs.openSync(filePath, 'r');
  fs.readSync(fd, buf, 0, 24, 0);
  fs.closeSync(fd);
  if (buf[0] === 0x89 && buf[1] === 0x50) {
    return { w: buf.readUInt32BE(16), h: buf.readUInt32BE(20) };
  }
  return null;
}

function addImage(slide, filePath, opts) {
  if (!fs.existsSync(filePath)) return;
  const dim = pngSize(filePath);
  const o = { path: filePath, ...opts };
  if (dim) {
    const ar = dim.w / dim.h;
    if (o.w && !o.h) o.h = +(o.w / ar).toFixed(3);
    if (o.h && !o.w) o.w = +(o.h * ar).toFixed(3);
  }
  slide.addImage(o);
}
```

Usage — always provide only `w` OR `h`, never both:

```javascript
// Large logo on title slide — CSS says width: 190px, let height auto-calculate
addImage(slide, 'assets/logo-large.png', { x: inch(153), y: inch(106), w: inch(190) });

// Small logo in footer — CSS says height: 35px, let width auto-calculate
addImage(slide, 'assets/logo-small.png', { x: W - 0.85, y: H - 0.59, h: inch(35) });
```

### Speaker notes

```javascript
slide.addNotes('Talk about the 40% stat. Mention the Minnesota study.');
```

Extract from `<div class="notes">` inside each `.slide` element.

### Save

```javascript
await pptx.writeFile({ fileName: 'output.pptx' });
```

## How to Map HTML → PPTX

When reading the HTML, follow this process for each `<div class="slide ...">`:

1. **Detect background**: `slide-title-bg` → `bg-title.png`, `slide-content-bg` → `bg-content.png`
2. **Walk child elements** in DOM order. For each element, extract:
   - **Position**: from CSS classes or inline `style` attributes (`top`, `left`, `bottom`, `right`, `width`, `height`). Convert all px values with `inch()`.
   - **Typography**: `font-size` → `pts()`, `font-weight: bold` or `700` → `bold: true`, `color` → strip to hex.
   - **Text content**: preserve inline formatting (`<strong>`, `<b>`, `<em>`, styled `<span>`) as text runs.
3. **Convert `bottom` positioning**: PPTX only uses top-left origin. `y = H - inch(bottom) - elementHeight`.
4. **Convert `right` positioning**: `x = W - inch(right) - elementWidth`.
5. **Handle two-column layouts**: look for CSS `grid-template-columns`, `display: flex` with side-by-side children, or similar patterns. Calculate x positions for left column (`x0`) and right column (`x0 + colW + gap`).
6. **Handle `rgba()` colors**: pre-composite against the background color, or use `transparency` property on fill/text.

### Common HTML patterns to recognize

| HTML pattern | PPTX equivalent |
|---|---|
| `<h1>` at top of slide | `addText` with `bold: true`, large `fontSize`, positioned at top |
| `<strong>text</strong>` inside a paragraph | text run with `bold: true` — keep same color or use pink if the HTML has pink color |
| Large stat number (big font-size, accent color) | `addText` with large `fontSize`, `color: 'FC3165'`, positioned per inline style |
| `position: absolute` with explicit coordinates | Direct `x`, `y` mapping with `inch()` |
| `display: grid; grid-template-columns: 1fr 1fr` | Two text boxes side by side, each with `w = (W - margins - gap) / 2` |
| `background: rgba(...)` on a card | `addShape(RECTANGLE)` with `fill` + `transparency`, then `addText` on top |
| `border-left: 4px solid pink` | Thin `addShape(RECTANGLE)` as accent bar |
| `<ul><li>` lists | `addText` array with `bullet` options |
| `<div class="footer">` | "Confidential" text + small logo image (see below) |
| `<div class="notes">` | `slide.addNotes(text)` |

## Required Elements on Every Slide

### Footer (on every content slide, NOT on title slides)

```javascript
// "Confidential" text — right-aligned near bottom-right
slide.addText('Confidential', {
  x: W - 2.8, y: H - 0.59,
  w: 1.8, h: 0.35,
  fontSize: pts(20), fontFace: 'Montserrat',
  color: 'B3B4C2', align: 'right', valign: 'middle',
});

// Small DoiT logo — use addImage helper, provide ONLY h, width auto-calculates
addImage(slide, 'assets/logo-small.png', { x: W - 0.85, y: H - 0.59, h: inch(35) });
```

### Title slide logo

```javascript
// Use addImage helper, provide ONLY w, height auto-calculates from aspect ratio
addImage(slide, 'assets/logo-large.png', { x: inch(153), y: inch(106), w: inch(190) });
```

## Important Notes

- **Inspect inline styles**: The AI-generated HTML often uses inline `style` attributes for positioning and sizing. Always parse these — do not rely solely on CSS class names.
- **Preserve all text formatting**: Bold, color, and font-size differences within a single text block must use text runs, not separate `addText` calls (unless they're spatially separate).
- **Font sizes must match**: Always use `pts()` to convert CSS `font-size`. Never use arbitrary or estimated values.
- **Line height must match**: Always read the CSS `line-height` and set `lineSpacingMultiple` accordingly. Without this, text in PPTX will look cramped.
- **Padding must match**: Always read CSS `padding` on boxes/cards and set `margin` on the corresponding `addText`. Without this, text will be flush against box edges.
- **Never stretch images**: Always use the `addImage` helper with only `w` OR `h`, never both. The helper calculates the other from the actual PNG dimensions.
- **Font availability**: Montserrat must be installed on the machine opening the PPTX. Google Slides has it built in. Mention this to the user.
- **Slide dimensions**: Always use `defineLayout` with `width: 13.33, height: 7.5` — this matches the 16:9 ratio of the HTML canvas.
- **No hardcoded patterns**: Read the actual HTML. Different presentations will have different layouts. Your conversion script must be written specifically for the HTML you're looking at.

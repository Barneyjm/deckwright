# Deckwright — Authoring Reference

Everything needed to write slides for the template. Read this before editing `assets/template.html`.

## Contents
1. Mental model
2. Per-layout catalog (markup for every slide type)
3. Shared components
4. Fragments (build steps)
5. Speaker notes
6. Footer chrome
7. Editing the chart
8. Theming tokens
9. Adding images
10. Controls
11. Print to PDF
12. Caveats

---

## 1. Mental model

- The deck is a fixed **1280×720** canvas (`#stage`) that the engine scales to fit any window and letterboxes. Author as if every slide is exactly 1280×720.
- Each slide is one `<section class="slide …">` inside `<div id="stage">`. The engine finds them in document order, shows one at a time, and numbers them automatically.
- A base `.slide` is a vertically-centered flex column with generous padding (the "safe area"). Layout modifier classes change the composition.
- You write **content**. You do not write CSS or JavaScript except the documented theming tokens and the chart data array.

A slide skeleton:
```html
<section class="slide slide--split">
  <div class="eyebrow">Kicker</div>
  <h2 class="deck-title">The claim.</h2>
  <!-- body content -->
  <aside class="notes">Speaker note.</aside>
</section>
```

---

## 2. Per-layout catalog

### Title — `slide--title`
Opens the deck. Bottom-anchored. Carries `no-foot` (no footer) and `data-transition="none"`.
```html
<section class="slide slide--title no-foot" data-transition="none">
  <div class="eyebrow">Audience · Context</div>
  <h1 class="deck-title">The title, with one <em>italic</em> accent.</h1>
  <p class="lede">A one-sentence framing.</p>
  <div class="meta">
    <div>Prepared by<b>Name / team</b></div>
    <div>For<b>Audience</b></div>
    <div>Date<b>Month 2026</b></div>
  </div>
</section>
```
`meta` items are label-then-`<b>`value pairs; use as many as read well (2–4).

### Section divider — `slide--section`
Dark slide with a giant numeral. Use to chunk the deck. Carries `no-foot`.
```html
<section class="slide slide--section no-foot">
  <div class="sec-no">01</div>
  <div class="eyebrow">Movement name</div>
  <h2 class="deck-title">Declarative section line.</h2>
</section>
```
Set `sec-no` manually (`01`, `02`, …).

### Content + build — `slide--split` with `ul.bullets`
A claim with framing prose on the left and points on the right.
```html
<section class="slide slide--split">
  <div class="eyebrow">Theme</div>
  <h2 class="deck-title">A claim worth a slide.</h2>
  <div class="body">
    <div class="col"><p>Framing prose, a few short lines.</p></div>
    <div class="col">
      <ul class="bullets">
        <li class="frag"><b>Point one</b> — detail.</li>
        <li class="frag"><b>Point two</b> — detail.</li>
      </ul>
    </div>
  </div>
</section>
```
The `body` is a two-column grid. Either column can hold prose, bullets, or panels. For a single full-width column of bullets, you can place `ul.bullets` directly in the slide without `.body`.

### Statement / pull-quote — `slide--statement`
One large serif sentence. The deck's pivot. Carries `no-foot`.
```html
<section class="slide slide--statement no-foot">
  <blockquote class="statement">One sentence you want to <span class="hl">land</span> and stay.</blockquote>
  <div class="cite">Label<b>Source · role</b></div>
</section>
```
`.hl` accents a phrase. `cite` is optional.

### KPI row — `.kpi-row`
Two to four headline numbers in a ruled row.
```html
<section class="slide">
  <div class="eyebrow">The numbers</div>
  <h2 class="deck-title">Lead with the figures.</h2>
  <div class="kpi-row">
    <div class="kpi"><div class="num">3.2<span class="u">x</span></div><div class="lab">Caption.</div></div>
    <div class="kpi"><div class="num">87<span class="u">%</span></div><div class="lab">Caption.</div></div>
    <div class="kpi"><div class="num">$1.4<span class="u">M</span></div><div class="lab">Caption.</div></div>
  </div>
</section>
```
`.u` is the small unit suffix (`x`, `%`, `M`, `pts`). For two or four KPIs, set the grid columns by editing the `.kpi-row` rule's `grid-template-columns` token, or just let three sit comfortably. Numbers use tabular figures and align automatically.

### Chart — `.chart-wrap` + `#chart`
One chart on the left (drawn as SVG by the engine), a legend on the right. There is **one** `#chart` per deck by default — see §7 to change the data.
```html
<section class="slide">
  <div class="eyebrow">The trend</div>
  <h2 class="deck-title">A chart, no library.</h2>
  <div class="chart-wrap">
    <div class="chart" id="chart"></div>
    <div class="legend">
      <div class="row"><span class="sw" style="background:var(--line)"></span><p><b>Series A</b> — baseline.</p></div>
      <div class="row"><span class="sw" style="background:var(--accent)"></span><p><b>Series B</b> — the story.</p></div>
    </div>
  </div>
</section>
```

### Two panels — `slide--split` with `.panel`
Side-by-side boxed comparison.
```html
<div class="body">
  <div class="col panel"><h3>Option A</h3><p>Self-contained idea.</p></div>
  <div class="col panel"><h3>Option B</h3><p>Parallel structure.</p></div>
</div>
```

### Dark — add `slide--dark`
Append `slide--dark` to **any** content layout to invert it to the ink background (text, eyebrow, and keyline recolor automatically). Use sparingly for a turning point.
```html
<section class="slide slide--dark">
  <div class="eyebrow">Emphasis</div>
  <h2 class="deck-title">Invert for weight.</h2>
  <p class="lede">Loudest when rare.</p>
  <div class="keyline"></div>
</section>
```

### Closing — `slide--closing`
Dark sign-off. End on the takeaway or the ask, not "Thank you". Carries `no-foot`.
```html
<section class="slide slide--closing no-foot" data-transition="none">
  <div class="eyebrow">In summary</div>
  <h2 class="deck-title">The one thing to remember.</h2>
  <p class="lede">Or the ask.</p>
  <div class="meta">Contact · next step</div>
</section>
```

---

## 3. Shared components

- `.eyebrow` — small uppercase kicker with a leading rule. One per slide, above the title.
- `.deck-title` — the serif headline. `h1` is title-size; `h2` is standard. Wrap one or two words in `<em>` for an italic-serif accent.
- `.lede` — the supporting sentence under a title.
- `ul.bullets` — disciplined bullet list; bold the lead-in of each `li`.
- `.keyline` — a short accent rule for breathing room.
- `.muted` — secondary/caption color.
- `<em>` renders italic serif anywhere; `<strong>`/`<b>` is a heavier weight.

---

## 4. Fragments (build steps)

Add `class="frag"` to any element. On each advance it appears in document order; reversing hides it. Entering a slide forward starts with none shown; entering backward shows all. Use to build an argument, not to animate decoration.

---

## 5. Speaker notes

Put `<aside class="notes">…</aside>` inside any slide. It never renders on the slide. Press `S` while presenting to open a notes panel showing the current slide's note and position. Notes are also hidden in the printed PDF.

---

## 6. Footer chrome

A hairline progress bar, a brand label, and an auto page number sit at the bottom of content slides. Edit the brand text in the chrome block (`<span class="brand">`). Slides with `no-foot` hide it (title, section, statement, closing already have it). Press `C` to toggle all chrome live.

---

## 7. Editing the chart

The chart is plain SVG generated by `drawChart()` near the bottom of the file — no library, fully offline. To change it, edit the `groups` array:
```js
const groups=[['Build',38,9],['Fix fonts',12,0],['Re-export',16,1],['Send',6,1]];
//             [label, seriesA value, seriesB value]
const max=45; // y-axis top — set above your largest value
```
Each entry is a labeled pair of bars (grey baseline + accent). Adjust `max` to the axis ceiling. For a single-series bar chart, set the second value to 0 (or remove the second `rect` in the loop). For more than ~6 groups, reduce `bw` (bar width). If a deck needs multiple distinct charts, duplicate `drawChart` under a new id and call it; keep all drawing inline so the file stays self-contained.

---

## 8. Theming tokens

All look-and-feel lives in `:root` at the top of the `<style>` block. Edit these, not individual slides.

```css
--ink:#14181f;        /* primary text + dark slides   */
--paper:#f4f1ea;      /* warm background              */
--accent:#b8472d;     /* the single editorial accent  */
--accent-ink:#d9764f; /* accent legible on dark slides*/
--muted:#717a86;      /* captions / secondary text    */
--line:#d3ccbd;       /* hairlines + chart baseline   */
--letterbox:#0b0d11;  /* space around the canvas      */
--sans: …;            /* UI / body / data             */
--serif: …;           /* headlines / quotes           */
--dur:480ms;          /* transition speed             */
```

To rebrand: change `--accent` (and `--accent-ink` to a lighter tint of it) and optionally `--ink`/`--paper`. Keep contrast high. The embedded fonts are referenced by `--sans`/`--serif`; replacing a font means embedding a new one as base64 `@font-face` — only do that if asked, and never link a remote font.

---

## 9. Adding images

To stay self-contained, embed images as base64 data URIs, not file paths or URLs:
```html
<img src="data:image/jpeg;base64,/9j/4AAQ…" alt="">
```
For a full-bleed image slide, place the `<img>` to cover the slide with `style="position:absolute;inset:0;width:100%;height:100%;object-fit:cover"` and lay text over it. Keep embedded images reasonably compressed so the file stays portable.

---

## 10. Controls

`→` / Space / clicker → next or next build step · `←` back · `Home`/`End` first/last · `F` fullscreen · `S` speaker notes · `B` blackout · `C` toggle footer · `?` help overlay · `Esc` closes overlays. Tablet: tap right/left half, or swipe.

---

## 11. Print to PDF

In Chrome: `⌘P` / `Ctrl+P`, set **Landscape** and **Margins: None**. The print styles lay out one slide per page at full size with all fragments shown and overlays hidden — a clean handout.

---

## 12. Caveats

- **Deep-linking** (`#3` in the URL) works in a real browser tab and the standalone file; inside a sandboxed preview iframe it's silently skipped because the origin is opaque. Navigation itself is unaffected.
- **Dual-monitor presenter view** (notes on one screen, slides on the other) is not achievable from one self-contained file; the `S` panel is for rehearsal. reveal.js is the fallback if that's a hard requirement.

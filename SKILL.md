---
name: deckwright
description: Build polished, self-contained HTML slide decks that look and behave like a professional PowerPoint or Keynote presentation but ship as a single portable .html file. Use this skill whenever the user wants to create a presentation, slide deck, pitch deck, board update, executive briefing, investor or sales deck, or "slides" of any kind — and especially when they want it to look high-end, present full-screen with arrow-key or clicker navigation, work offline, avoid PowerPoint, or hand it over as one file or link. Also use it when turning a report, memo, or set of talking points into slides. Produces one ready-to-present HTML file with embedded fonts, a fixed 16:9 scaling canvas, build animations, speaker notes, and print-to-PDF.
license: MIT
---

# Deckwright

Generate an executive-grade slide deck as a **single self-contained `.html` file**. The goal: someone watching it presented full-screen cannot tell it isn't PowerPoint or Keynote — but it travels as one file, opens in any browser, and needs nothing installed.

## How it works

The hard part — the presentation engine and the design system — is already built and lives in `assets/template.html`. That file contains an embedded font stack, a fixed 1280×720 canvas that scales to any screen, keyboard/clicker/touch navigation, build animations, speaker notes, and print styling. **You do not rebuild any of that.** You copy the template and replace the slide content.

A finished, polished deck lives in `examples/deckwright-overview.html` — skim it to see what good output looks like before you start.

### Workflow

1. **Copy the template** to the output location, named for the deck:
   ```bash
   cp assets/template.html <deck-name>.html
   ```
   (In environments with a dedicated outputs directory, copy it there.)
2. **Read `references/authoring.md`** before writing slides. It is the full catalog of layouts, components, theming tokens, chart editing, and controls. Don't guess at class names — they're all listed there.
3. **Replace the slides.** Each slide is one `<section class="slide ...">…</section>` inside `<div id="stage">`. Delete the placeholder slides and write your own using the layouts in the reference. Add, remove, or reorder freely — the engine discovers slides and renumbers automatically.
4. **Edit the small bits of chrome**: the footer brand text (`<span class="brand">`) and, if there's a chart, the data array inside `drawChart()` near the bottom of the file.
5. **Deliver the file** and give a one-line summary plus the key controls (below). Hand back the single `.html` file — open it in a browser to present.

### The three rules that keep it presentation-grade

These are why it passes for native software. Don't break them:

- **Keep it self-contained.** Never add `<link>` tags to Google Fonts, CDNs, external CSS/JS, or remote images. The fonts are already embedded; everything must render with zero network requests so it looks identical offline and on any machine. For images, embed them as base64 data URIs.
- **Don't touch the engine or the `<style>` block** except the documented theming tokens in `:root`. Rewriting the engine is how decks start looking like web pages instead of slides.
- **Respect the fixed canvas.** Author against 1280×720. Don't fight the layout with absolute pixel hacks; use the provided layout classes, which already sit on a proper grid with safe margins.

## Slide vocabulary (quick reference)

Full detail and every component is in `references/authoring.md`. The layouts, by `class`:

| Layout | Class | Use for |
|---|---|---|
| Title | `slide--title` | Opening slide |
| Section divider | `slide--section` | Chunking the deck; large numeral |
| Content + build | `slide--split` + `ul.bullets` | A claim with supporting points (use `frag` to reveal) |
| Statement | `slide--statement` | A single large pull-quote / the pivot idea |
| KPI row | `.kpi-row` | Two to four headline numbers |
| Chart | `.chart-wrap` + `#chart` | One hand-drawn SVG chart |
| Two panels | `slide--split` + `.panel` | Side-by-side comparison |
| Dark | add `slide--dark` | Inverting any content layout for emphasis |
| Closing | `slide--closing` | The takeaway or the ask |

Reveal-on-click: add `class="frag"` to any element. Speaker notes: put them in an `<aside class="notes">` inside the slide. Slides that should hide the footer (title, section, closing) carry `no-foot`.

## Content discipline

The design only works if the writing is disciplined. Treat each slide as a cue card, not a page.

- **One idea per slide.** If a slide has two arguments, it's two slides.
- **Short lines.** Headlines are claims, not topics ("Margins recovered in Q3", not "Q3 Margins"). Keep prose to a few short lines; the speaker fills in the rest.
- **Lead with the number.** Anything quantitative belongs on a KPI or chart slide where the figure dominates.
- **Use sections to pace.** A divider every 3–5 slides resets attention.
- **Use `frag` sparingly** — to build an argument or reveal a punchline, not to animate everything.
- **Match the deck to the room.** For an executive or board audience, keep it spare and confident; fewer words, bigger type, more whitespace.

If the user gives you raw material (a report, notes, bullet points), do the editorial work: pull out the throughline, group it into sections, turn topics into claims, and surface the numbers — don't just paste paragraphs onto slides.

## Theming

The entire look is driven by CSS variables in `:root` (palette, fonts, motion). To match a brand or change the mood, edit those tokens — see the theming section of `references/authoring.md`. Don't restyle individual slides ad hoc.

## Controls (tell the user these)

`→` / Space / clicker advance and step through builds · `←` back · `F` fullscreen · `S` speaker notes · `B` black out · `C` hide footer · `Home`/`End` jump · `?` help · `⌘P` (Chrome, landscape, no margins) saves a clean PDF. On a tablet: tap right/left or swipe.

## Caveats to mention when relevant

- **URL deep-linking** to a slide (`#3`) works in a normal browser tab or the standalone file, but is silently skipped inside sandboxed preview iframes (opaque origin) — navigation still works fine.
- **True dual-monitor presenter view** (private notes on the laptop, slides on the projector) isn't possible from a single self-contained file. The `S` notes panel is for rehearsal. If the user genuinely needs dual-screen presenter view, point them to reveal.js as the heavier alternative — at the cost of single-file portability.

## Output

Always deliver one `.html` file. Don't split into multiple files, and don't also produce a `.pptx` unless asked — the single portable file is the point.

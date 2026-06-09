# deckwright

Build presentation-grade slide decks as a **single self-contained `.html` file** — no PowerPoint, no install, nothing to render but a browser. Presented full-screen, it’s meant to be indistinguishable from native slide software, while staying a versionable, scriptable source you own.

This repo is a [Claude skill](https://docs.claude.com): when installed, asking Claude for a deck, pitch, or board update produces one portable HTML file automatically.

## What makes it presentation-grade

- **Fixed 1280×720 canvas** that scales to any screen instead of reflowing like a web page — the layout is pixel-locked like PowerPoint.
- **Fonts embedded in the file**, so type never substitutes on someone else’s machine.
- **Zero external requests** — renders identically offline, forever.
- Arrow-key / clicker / touch navigation, build animations, speaker notes, and print-to-PDF, all in one file.

## Repo layout

```
deckwright/
├── SKILL.md                      # instructions Claude follows to generate decks
├── assets/
│   └── template.html             # the engine + design system (this IS the framework)
├── references/
│   └── authoring.md              # full layout, component, theming, and chart catalog
└── examples/
    └── deckwright-overview.html  # a finished deck — "what good looks like"
```

The `.skill` distributable is a build artifact, not source — see below.

## Use it as a skill

Install by uploading the built `.skill` file in Claude (Settings → Skills/Capabilities), or drop this folder into a Claude Code skills directory. After that, “make me a board deck on X” pulls the engine in automatically.

## Use it by hand

1. Copy `assets/template.html` to a new file.
1. Each slide is one `<section class="slide …">` inside `<div id="stage">`. Replace the placeholder slides with your own; the engine renumbers automatically.
1. Edit the footer brand text and, if you use the chart, the `groups` array in `drawChart()` near the bottom.
1. Open in any browser. `F` fullscreen, `→`/`←` navigate, `S` speaker notes, `?` for all controls, `⌘P` (landscape, no margins) for a clean PDF.

See `references/authoring.md` for every layout (`slide--title`, `--section`, `--statement`, `--kpi`, `--split`, `--dark`, `--closing`), the `frag` build system, and the `:root` theming tokens. `examples/deckwright-overview.html` is a complete deck you can open to see the output.

## Build the `.skill`

The `.skill` is just a zip of this folder. Rebuild it on each release; don’t commit it.

```bash
# from the parent directory
zip -r deckwright.skill deckwright -x '*/.git/*' '*.skill' '*/.DS_Store'
```

(Or use Anthropic’s `skill-creator` packaging script if you have it, which validates first.)

## License

MIT — see <LICENSE>.

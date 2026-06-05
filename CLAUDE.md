# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Source for <https://ai.thanhtt.com> — a small static site hosting Vietnamese technical slide decks about Agentic AI. There is **no build step, no package manager, no tests, and no framework**. Everything is plain HTML/CSS/JS committed as-is and served directly by Cloudflare Pages.

Content is in Vietnamese. Preserve tone and terminology when editing copy.

## Running locally

Open a file directly in a browser, or serve the folder statically:

```bash
python3 -m http.server 8000   # then visit http://localhost:8000
```

There is nothing to compile or watch — edit a file, reload the page.

## Layout

- `index.html` — the landing page. **Hand-authored** with its own design system (Inter + JetBrains Mono, CSS custom properties under `:root`, semantic class names like `.hero`, `.card`). Edit this like normal responsive HTML/CSS.
- `vibe-code/index.html` and `agent-harness/index.html` — the two **decks**. Each is a single self-contained file: inline CSS, inline JS, and base64-embedded images, no shared assets. They are **generated exports from slide software**, not hand-written — see below.
- `assets/favicon.svg` — only shared static asset.
- `_headers`, `robots.txt`, `sitemap.xml` — Cloudflare Pages config and SEO. **Update all three when adding/removing a deck** (cache rule in `_headers`, `<url>` in `sitemap.xml`).

## How the decks work

A deck is a fixed-canvas slideshow. Each `<section class="slide" data-n="N">` is one slide, sized to a fixed pixel canvas, with every element absolutely positioned (`left/top/width/height` in `px`, font sizes in `pt`). The inline `<script>` at the bottom:

- collects `.slide` elements, shows one at a time via the `.active` class,
- scales the whole deck to fit the viewport with `transform: scale(min(vw/W, vh/H))`,
- drives navigation by keyboard (← → Space PageUp/Down Home End, `F` fullscreen), click (left/right half), touch swipe, and `#N` URL hash.

**The two decks use different canvas sizes** — this matters because all coordinates are absolute:
- `vibe-code`: **1280×720** (scale factor hardcoded as `/1280` `/720`)
- `agent-harness`: **960×540** (`const W=960, H=540`)

### Editing slide content

- Text edits inside a slide are safe — find the `<span>` and change the copy.
- Layout changes are fragile: positions are absolute and tuned to the canvas, so moving/resizing one box does not reflow others. Adjust `left/top/width/height` deliberately.
- To add a slide, copy a full `<section class="slide" data-n="...">` block, renumber `data-n` and the visible page-number span, and keep it within the deck's canvas dimensions. The nav script counts slides automatically.
- Because these are tool exports, prefer regenerating from the source slides over large hand-edits when possible; reserve manual edits for small fixes.

## Conventions

- Keep decks self-contained: no external JS/CSS/image files, no shared stylesheet. Inline everything (images as `data:` URIs).
- Match the existing style of whichever file you touch — the landing page and the decks are intentionally different design systems; don't cross-pollinate.

# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Vanilla JavaScript Tetris game using HTML5 Canvas. Zero dependencies, no build step, no package.json. UI text is in Spanish.

## How to Run

Open `index.html` directly in a browser, or serve with any static server:

```bash
python3 -m http.server 8000
# or: npx serve .
```

No build, install, or compile step exists.

## Architecture

Three files, tightly coupled:

- **`index.html`** — DOM structure with two `<canvas>` elements (board 300x600, next-piece preview 120x120), side panel (score/lines/level/controls), and game-over/pause overlay.
- **`style.css`** — CSS custom properties on `:root` for theming. Dark theme is default; light theme via `[data-theme='light']`. Theme persisted in `localStorage`.
- **`game.js`** — All game logic in one file (~325 lines). Key parts:
  - Board is a `ROWS(20) × COLS(10)` matrix of color indices (0 = empty, 1-8 = piece type)
  - 8 piece types: standard 7 (I/O/T/S/Z/J/L) plus a custom "Nut" piece (index 8, 3x3 ring shape)
  - Game loop via `requestAnimationFrame`, gravity driven by `dropAccum` vs `dropInterval`
  - Wall kicks on rotation: tries offsets `[0, -1, 1, -2, 2]`
  - Ghost piece rendered at `globalAlpha = 0.2`
  - Scoring: `[0, 100, 300, 500, 800] × level`; level increments every 10 lines; speed = `max(100, 1000 - (level-1)*90)` ms

## Key Constraints

- Canvas dimensions must match `COLS × BLOCK` and `ROWS × BLOCK` (currently 300×600 with BLOCK=30). If grid constants change, update the `<canvas>` `width`/`height` in `index.html`.
- Theme toggle reads `data-theme` attribute on `<html>` and uses `localStorage`. Grid line color is read from computed CSS `--grid` variable at draw time.
- No module system — everything is global in `game.js`. Order of DOM element lookups at top of file matters (runs on `<script>` load at end of `<body>`).

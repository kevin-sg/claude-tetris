# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the game

No build step required. Open directly in a browser:

```bash
xdg-open index.html          # Linux
# or serve with any static server:
python3 -m http.server 8000
```

## Architecture

Single-page vanilla JS app — no framework, no bundler, no dependencies.

- **`index.html`** — DOM structure: `<canvas id="board">` (300×600 px) for the playfield, `<canvas id="next-canvas">` (120×120 px) for piece preview, sidebar HUD (`#score`, `#lines`, `#level`), and `#overlay` for pause/game-over states.
- **`style.css`** — Dark/retro aesthetic. Overlay visibility is toggled via `.hidden` class.
- **`game.js`** — All game logic (~300 lines). Key concepts:

  | Area | Detail |
  |---|---|
  | Board model | `ROWS × COLS` matrix; `0` = empty, `1–7` = piece color index |
  | Pieces | Defined as square matrices in `PIECES[]`; rotated via transpose+row-reverse (`rotateCW`) |
  | Collision | `collide(shape, ox, oy)` checks bounds and occupied cells |
  | Wall kicks | `tryRotate()` tries offsets `[0, -1, 1, -2, 2]` before discarding rotation |
  | Game loop | `requestAnimationFrame`-based; accumulates `dt` and drops piece when `dropAccum ≥ dropInterval` |
  | Line clear | `clearLines()` scans bottom-up, splices full rows, unshifts empty row at top |
  | Speed | `dropInterval = max(100, 1000 − (level−1) × 90)` ms; level increments every 10 lines |
  | Ghost piece | `ghostY()` projects piece downward; drawn at `globalAlpha = 0.2` |
  | Scoring | Classic table `[0, 100, 300, 500, 800]` × level; soft drop +1/row, hard drop +2/cell |

  Global mutable state lives in module-level `let` variables (`board`, `current`, `next`, `score`, `lines`, `level`, `paused`, `gameOver`, `animId`, etc.). `init()` resets all of them.

## Tunable constants in `game.js`

`COLS`, `ROWS`, `BLOCK`, `COLORS`, `LINE_SCORES`. If `COLS`, `ROWS`, or `BLOCK` change, the `width`/`height` attributes on `<canvas id="board">` in `index.html` must be updated to match (`COLS × BLOCK` and `ROWS × BLOCK`).

# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the project

No build step. Open `tictactoe.html` directly in a browser:

```bash
open tictactoe.html
```

## Architecture

The entire project is a single self-contained file: `tictactoe.html`. It has no dependencies, no bundler, and no server. CSS, SVG artwork, and JavaScript all live inline.

**JS structure inside the `<script>` block:**

- `CARROT_SVG` / `GRAPE_SVG` — inline SVG strings injected into cells via `innerHTML` when a move is placed. Carrot = player (`'X'`), Grape = CPU (`'O'`). The board state still uses `'X'`/`'O'` as markers internally; the SVGs are purely presentational.
- `init()` — resets board state, randomly picks who goes first, triggers a delayed CPU move if CPU goes first. Scores persist across `init()` calls.
- `place(idx, mark)` — writes to `board[]`, sets `innerHTML`, and adds CSS classes (`carrot`/`grape`, `taken`).
- `checkWinner(b)` — pure function, accepts any board array, returns `{ winner, line }` or `null`. Used by both the live game and the minimax search.
- `cpuMove()` — dispatches to one of three strategies based on `difficulty`:
  - `easy`: 80% random, 20% `bestMove`
  - `medium`: `winOrBlock` (one-move lookahead) then random
  - `hard`: full `minimax` (unbeatable)
- `minimax(b, isMax)` — standard recursive minimax, no alpha-beta pruning (acceptable at depth ≤ 9).
- `boardLocked` — boolean flag that blocks player clicks while the CPU's artificial delay (`setTimeout`) is in flight.

**CSS conventions:**

- Apple design tokens used throughout: `#f5f5f7` background, `#1d1d1f` text, `#FF9500` orange (carrot/player), `#AF52DE` purple (grape/CPU).
- Win state is applied by adding class `win` to cells; `.carrot.win` and `.grape.win` each have distinct glow colors.
- Font stack: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'Helvetica Neue', sans-serif`.

## Git workflow

All changes should be committed with a clean message and pushed:

```bash
git add tictactoe.html
git commit -m "short description of what changed and why"
git push
```

Remote is `origin` → `https://github.com/o-oruk/tic-tac-toe` on branch `main`.

## Changing piece artwork

To swap the player or CPU piece, replace the `CARROT_SVG` or `GRAPE_SVG` template literal (lines ~238–294). Both use `viewBox="0 0 80 80"`. Update the matching CSS win-glow colors (`.carrot.win`, `.grape.win`) and the score/badge color tokens (`#FF9500`, `#AF52DE`) to match the new artwork's palette.

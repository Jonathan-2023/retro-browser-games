# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A collection of retro-style browser games. Each game is a **single self-contained HTML file** — no build tools, no dependencies, no server required. Open any file directly in a browser to play.

## Repository Conventions

- **One file per game** — HTML, CSS, and JS all live in one `.html` file.
- **No external dependencies** — everything is vanilla JS and HTML5 Canvas (or DOM for simpler games).
- **Retro dark aesthetic** — dark backgrounds, limited color palette, monospace fonts, pixel-art style via canvas primitives (no image assets).
- **Git workflow** — commit and push to `origin/master` (via SSH) frequently throughout all work sessions. Never leave a working state uncommitted. Use Conventional Commits: `feat:`, `fix:`, `chore:`. Push after every commit so work is never lost and any state can be reverted via GitHub.

## Games

| File | Rendering | Description |
|------|-----------|-------------|
| `tictactoe.html` | DOM/CSS | Two-player Tic Tac Toe with score tracking |
| `shooter.html` | HTML5 Canvas | Top-down wave shooter |
| `chess.html` | HTML5 Canvas | Chess with 1-player AI (minimax depth-3) and 2-player modes |

## shooter.html Architecture

The game loop runs via `requestAnimationFrame`. State is reset entirely by `initState()`.

**Execution order each frame:**
1. `update(dt)` — input → player movement → bullets → enemies → collisions → particles → wave timer
2. `render()` — floor tiles → bullets → enemies → player → particles → vignette → HUD → overlays

**Key data structures:**
- `player` — position, hp, aimAngle (tracks mouse via `atan2`), walk animation state
- `bullets[]` — `{x, y, vx, vy, life}`, filtered out when `life <= 0` or off-screen
- `enemies[]` — `{x, y, type, hp, speed, r, dead, deathTimer, ...}`, all types defined in `ENEMY_CFG`
- `particles[]` — `{x, y, vx, vy, life, r, color}`, used for hit sparks and death explosions

**Enemy types** (`ENEMY_CFG`): `walker` (slow/red), `runner` (fast/orange), `tank` (3HP/purple). Type distribution in `spawnEnemy()` shifts with `wave` number.

**Collision** is circle-circle distance checks (no spatial partitioning — fine for this scale).

**Timers** use two conventions — watch for this when modifying:
- `muzzleTimer` counts down in *frames* (decremented once per `update` call)
- `invTimer`, `hitTimer`, `waveTimer`, `waveDisplay`, `deathTimer` count down in *milliseconds* (multiplied by `dt * 1000`)

## Adding a New Game

1. Create `mygame.html` as a self-contained file following the same single-file pattern.
2. Keep all rendering via canvas primitives or DOM — no image files.
3. Commit: `git add mygame.html && git commit -m "feat: add <game name>"` then `git push`.

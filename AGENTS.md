# AGENTS.md

## Project facts
- Static browser game (no package manager, bundler, lint, test, or build config found).
- Runtime files: `index.html` loads `game.js`; rendering uses a single 800x600 `<canvas>` (`index.html`, `game.js`).
- `README.md` is not fully authoritative (e.g. it mentions power-ups/unique asteroid types not present in current code); prefer `index.html`/`game.js`.

## Run / verify
- Run locally: open `index.html` directly, or serve the folder (README suggests `npx serve .` then visit `http://localhost:3000`).
- No automated test/lint/typecheck commands are configured in this repo; manual verification in-browser is the practical check.

## Code structure (`game.js`)
- Single-file implementation with global state and classes: `Bullet`, `Asteroid`, `Ship`, `Particle`.
- Main flow: `initGame()` -> `requestAnimationFrame(loop)`; `loop` computes `dt`, calls `update(dt)` then `draw()`.
- Game states: `'playing' | 'dead' | 'gameover'` (restart on Space from game over).
- Core helpers/constants live near top: `keys`/`justPressed`, `wrap/dist/rand`, canvas size `W/H`.

## Conventions & gotchas
- Game is frame-rate independent via `dt` (clamped to `0.05s` in `loop`); preserve `dt`-based movement/timers when changing behavior.
- Space is toroidal: positions wrap with `wrap(..., W/H)`; collisions use center-distance checks.
- Entity lifecycle uses mutable arrays filtered by `.dead` flags (and spawning new asteroids/particles during updates).
- User-facing HUD/overlay strings are in Spanish (e.g. `NIVEL`, `GAME OVER`, `PUNTAJE`); `index.html` is `lang="es"`.
- Keep changes dependency-free unless explicitly requested; match existing plain JS/canvas style.
- `README.md` has some encoding artifacts in text; avoid propagating them when editing docs.

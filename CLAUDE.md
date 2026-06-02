# ZETTEL

A party word-guessing game (German UI, Activity/Tabu-style). 2–6 teams take turns
describing/acting out words across editable phases (default: Erklären, Pantomime, Ein Wort, Ein Ton).
Designed for a single phone passed around the group.

## Project layout
- **Everything lives in `index.html`** — HTML + CSS + vanilla JS in one file. No build,
  no dependencies, no framework.
- `.nojekyll` + deployed as a static page (GitHub Pages). Just open `index.html` in a
  browser to run it.

## Architecture (all in the `<script>` in index.html)
- Single global `state` object, persisted to `localStorage` (`save()` / `load()`).
- `render()` is a router that switches on `state.screen` and rebuilds `#app`'s innerHTML.
- Screens: `lobby` → `addPlayer` / `editWords` → `teams` → `game` → `final`.
  In `game`, `state.game.phase` is `ready` / `playing` / `turnResult` / `subphaseDone`.
- Inline `onclick` handlers must be exposed on `window` (see the `Object.assign(window, …)`
  block near the bottom).
- Rounds ("phases") live in `state.settings.phases` (`{name, timer}`), seeded from
  `DEFAULT_PHASES` and editable in the lobby (rename / reorder / add / delete). Access via
  the `phases()` helper. Words come from `allWords()` (all players' words).
- Teams: `state.settings.numTeams` (2–`MAX_TEAMS`); `createTeams()` distributes shuffled
  players round-robin. Each team is `{name, color(hex), order, pointer, score}`. Colours come
  from `TEAM_PALETTE` and are applied via a `--tc` CSS custom property (with `color-mix`),
  not per-colour classes — so any number of team colours works.

## Conventions
- UI text is German.
- Always `esc()` user-provided strings before putting them in HTML.
- Drag & drop and other touch interactions use pointer events (mobile-first, portrait).

## Verifying changes
- No test suite. Sanity-check the JS parses, e.g.:
  `node -e "new Function(require('fs').readFileSync('index.html','utf8').match(/<script>([\s\S]*?)<\/script>/)[1].replace('\"use strict\";',''))"`
- Best real check is opening `index.html` in a browser and clicking through the flow.

# Eclipse

> **Designed and first built by April ([agongster](https://github.com/agongster)), who wrote [SPEC.md](SPEC.md) and the Phase 1 code. Continued in Phase 2 by Yubo Zhang ([zhangyubooo](https://github.com/zhangyubooo)).** The first commit in this repository is April's work, unmodified. See [What changed in Phase 2](#what-changed-in-phase-2) for my additions.

A two-player co-op puzzle game on one keyboard. Each player rotates (and zooms) their own half of a split screen, and paths that are impassable from one angle connect from another. Sol can hold down pressure plates that raise bridges, and Luna can walk on mist. Both have to reach their glowing rings, through a dawn meadow, a misty forest, the cosmos, and finally a field of drifting stones. See [SPEC.md](SPEC.md) for the full design and the acceptance criteria.

## Running it

Open `index.html` in a browser. There's no build step, no server and no dependencies. The Quicksand font loads from Google Fonts when you're online, and the game falls back to system fonts offline. Sound starts after the first Enter (or number key) press, because browsers block audio until a key is pressed.

Tested by playing in **Safari** on macOS (Phase 2), and in headless Chrome by April (Phase 1).

Tests (need Node):

```
node tests/world.test.js    # rules, abilities, sliding platforms and tips
node tests/levels.test.js   # every level can be solved and needs its mechanics (search-based solver)
```

## Controls

| Action           | Player 1 (Sol, warm, left half) | Player 2 (Luna, cool, right half) |
|------------------|---------------------------------|-----------------------------------|
| Move             | W A S D                         | Arrow keys                        |
| Rotate view      | Q / E                           | , / .                             |
| Zoom out / in    | Z / X, or scroll over your half | [ / ], or scroll over your half   |
| Start / continue | Enter (or Space)                | Enter (or Space)                  |
| Jump to a level (title screen) | 1 – 4             | 1 – 4                             |
| Restart level    | R                               | R                                 |
| Hints on/off     | H                               | H                                 |
| Sound on/off     | M                               | M                                 |
| Back to title    | Esc                             | Esc                               |

## What players see

- **Title screen:** the eclipse emblem, both players' controls and gifts, three lines on how to play, a level-select row ("Or jump to a level: 1 First Light · 2 Mist and Stone · …"), and "Press Enter to begin".
- **Level title card:** each level opens with its name and a one-line idea, e.g. "Sol's weight raises bridges. Luna is light enough to walk on mist."
- **Tutorial and tips:** a tip bar at the bottom of each half. On Level 1 it walks each player through moving, then rotating. After that it reacts to the situation, in [src/tutorial.js](src/tutorial.js):
  - "Hold still! Luna is on your bridge."
  - "Mist can't hold you. Only Luna can cross it."
  - "A hidden path connects from this angle…"
  - "The drifting stone is beside you. Step on before it slides away!" (Phase 2)
  - Otherwise it falls back to the level's own tip.
- **Key labels** around your own character show where each key goes:
  - **White:** a normal step.
  - **Gold:** crosses a hidden path.
  - **Red:** walks off the edge (including onto a sliding platform's empty end).
  - **Grey:** blocked by a height difference.
  - **Pale blue:** mist your character can't walk on.

  H hides the labels and tips.
- **HUD:** each half shows the player's name, the level, and a small dial with the view's current angle, zoom level and keys.
- **Zoom:** each player zooms their own half, from ×0.7 to ×2.8. As a view zooms in, its camera eases from the level's centre toward that player's own character, so you never zoom away from yourself. Zoom resets on each new level.
- **Feedback:**
  - A soft "bump" when a move is blocked.
  - A plate glows and its bridge rises (a dashed outline shows where it will appear).
  - Hidden-path ends twinkle until you find the right angle.
  - A sliding platform glides between two dashed outlines, and a soft whoosh plays when it carries someone (Phase 2).
  - When someone falls, their half shakes, the screen flashes red, and a banner says why, e.g. "The bridge vanished when Sol left the plate."
  - Finishing a level shows a small eclipse and the next level's name, and the motes of light rush upward.
- **End screen:** the moon slides over the sun, with the total time and number of falls. Enter returns to the title.
- **Themes:** each level has its own look.

  | Level | Theme | Platforms | Scenery | Background | Particles |
  |-------|-------|-----------|---------|------------|-----------|
  | 1 · First Light | Meadow at dawn | Grassy tops, clay sides | Trees, blossom trees, a rock, flowers, grass | Low sun, drifting clouds, rolling hills | Pollen |
  | 2 · Mist and Stone | Misty forest | Mossy tops, stone sides | Pines, a boulder, glowing mushrooms, grass | Light shafts, fog, layered pine silhouettes | Blinking fireflies |
  | 3 · Eclipse | Cosmos | Deep violet tops with twinkling stars, glowing edges | Crystals, a rune monolith, floating ringed orbs, stardust | Nebulae, starfield, a ringed planet, shooting stars | Stardust |
  | 4 · Afterglow (Phase 2) | Cosmos, dawn-tinted sky | Same as Level 3, plus a mint sliding platform | Crystals, orbs, stardust | Same as Level 3 | Stardust |

  In the cosmos, mist is drawn as violet stardust; the rules are the same.
- **Art and sound:**
  - A pastel gradient sky per level, and drifting motes of light.
  - Floating columns that fade into the sky, shimmering mist, light beams over the exits, and small robed figures with halos (a sun crown for Sol, a crescent for Luna).
  - Synthesized sound effects with no audio files: steps, rotations, plates, crossings, falls, platform rides and chimes.

## Acceptance criteria report (Phase 2)

Checked by me, not copied from the Phase 1 table. "Played" means I checked it by playing in Safari on macOS (all four levels completed); "code" means I read the code; "tests" means `tests/world.test.js` / `tests/levels.test.js`, which all pass.

| # | Criterion | Status | Notes |
|---|-----------|--------|-------|
| 1 | Title screen with controls; Enter starts Level 1 | **Met** | Played. |
| 2 | Split screen with divider | **Met** | Played. |
| 3 | Same level in both halves, distinct characters | **Met** | Played: Sol warm orange, Luna cool blue. |
| 4 | Player label and level number per half | **Met** | Played. Also shows a view-angle dial. |
| 5 | WASD / arrows, relative to each view | **Met** | Keys move along the isometric diagonals ("up" goes up-right), which is the closest an isometric grid can get. With the key labels it felt intuitive. *Unclear criterion:* "the direction it points" can't be met literally on an isometric grid, so the spec could say "along the nearest tile direction". |
| 6 | Simultaneous input | **Met** | Code: every key press is handled on its own, and one player's animation never blocks the other (`isPlayerBusy()` is per player). |
| 7 | Height differences block movement | **Met** | Tests and play. Stairs (Phase 1 extra) are the only way up or down one unit. |
| 8 | Glide between tiles (≈0.15 s); input ignored meanwhile | **Met** | `GLIDE_MS = 150` per tile; keys are ignored while gliding. |
| 9 | Independent rotation | **Met** | Played; tests check rotation is per view. |
| 10 | Four presses return to start | **Met** | Tests. |
| 11 | Illusion connections need the owner's rotation | **Met** | Tests ("partner's rotation doesn't open my connection") and play. |
| 12 | Rotation animates ≈0.3 s; only that player is locked | **Met** | `ROTATE_MS = 300`; played. |
| 13 | Sol on a plate raises its bridge; stepping off removes it | **Met** | Played (Levels 2, 3, 4) and tests. If Luna is on the bridge when it vanishes, she falls. |
| 14 | Luna on a plate does nothing | **Met** | Tests. |
| 15 | Luna walks on mist; Sol is blocked without falling | **Met** | Played and tests. |
| 16 | Falling restarts the level | **Met** | Played: both characters return to their starts and both views reset to 0°, after a ≈1.1 s fall animation. |
| 17 | R restarts | **Met** | Code: R calls the same reset as a fall. |
| 18 | "Level Complete" ≈2 s, then the next level | **Met** | It's 2.6 s (`LEVEL_COMPLETE_MS`), which felt right in play. |
| 19 | Exactly three levels with the required mechanics | **Met** | All three are completable (played, and the solver proves it) and need the mechanics listed. *Unclear criterion:* "exactly three levels" contradicts stretch #24, which adds a fourth. I read #19 as "three core levels" and changed the test to expect 4 levels. |
| 20 | End screen; Enter returns to title | **Met** | Played. It now appears after Level 4 instead of Level 3, as #24 implies. |
| 21 | Pastel palette, gradient background, distinct accents | **Met** | Played. |
| 22 | Ambient motion | **Met** | Played: the background keeps moving without affecting play. |
| 23 | Sounds for rotate, plate and level complete | **Met** | Played. On laptop speakers at low volume, only the level-complete chime was obvious at first; the rotate and plate sounds are much quieter but present. See Known Issues. |
| 24 | *(Stretch)* Level 4 with a sliding platform | **Met** | **Added in Phase 2.** "Afterglow": a platform swaps between two tiles every 2 s and carries anyone on it. The solver proves Level 4 is completable and impossible without the platform (and without its plate and its hidden path). Played to completion. |
| 25 | *(Stretch)* Level select | **Met** | **Added in Phase 2.** The title screen lists every level; pressing its number starts it. Played. |

## What changed in Phase 2

Everything was added on top of April's structure; none of her code was removed or rewritten. About 255 lines were added and 10 changed, across the files below. I followed her patterns: tiles go through `tileAt()`, level features are data in `levels.js`, `world.js` stays free of time and drawing, and every new mechanic gets tests.

- **Level select (#25):** `input.js` binds Digit1–9 to `{ selectLevel }`; `main.js` lets the title screen start any level (`startGame(now, levelIndex)`); `screens.js` draws the level row (`drawLevelSelect()`), built from `LEVELS`, so new levels appear automatically.
- **Sliding platforms (#24):**
  - `world.js`: `platforms` / `platformAtB` on the world, `platformPosition()`, `platformIndexAt()`, `shiftPlatforms()` (moves platforms and carries riders), and a new tile kind `"platform"` in `tileAt()`. `resetPositions()` also puts platforms back on their `a` end.
  - `main.js`: `PLATFORM_INTERVAL_MS = 2000` and `updatePlatforms()`, a clock that swaps the platforms. It pauses while someone is falling or the "Level Complete" banner is up, and restarts with the level.
  - `animation.js`: `startPlatformSlide()`, `startRide()` and `platformPose()`; the slide takes `PLATFORM_SLIDE_MS = 600`.
  - `render.js`: platforms are drawn as mint blocks over dashed outlines at both ends; `tileMetrics()` includes them so the level still fits on screen.
  - `audio.js`: a `ride` whoosh. `tutorial.js`: two platform tips.
- **Level 4 "Afterglow"** in `levels.js`.
- **Tests:** 10 platform rule checks in `world.test.js`. In `levels.test.js`, the solver now treats "wait for the platforms to swap" as a move, and there are 4 new Level 4 checks.

## Level solutions (spoilers)

- **1 · First Light (meadow):**
  - Both players weave between the trees to the staircase at the east edge of the meadow and climb to the terrace.
  - Each rotates to 90° and crosses east to the hilltop, then rotates to 180° and crosses south to the garden.
  - Sol's ring is at the garden's north-east corner, and Luna's at the south-east corner.
- **2 · Mist and Stone (forest):**
  - Sol climbs the stairs to the ridge and stops on the plate. Luna crosses the bridge that appears to the mossy island, goes around the boulder and onto the mist, then climbs the stairs to her outcrop.
  - Sol walks to the end of the ridge, rotates to 270° and crosses to his outcrop.
- **3 · Eclipse (cosmos):**
  - Sol climbs the stairs to the upper deck and stands on the first plate (south-east corner). Luna crosses the first bridge to the moon island.
  - Sol walks to the deck's north-east corner, rotates to 90° and crosses to the spire, then goes around the monolith to the second plate.
  - Luna crosses the second bridge onto the stardust, rotates to 180° and crosses north to her ledge.
  - Sol steps back to the middle of the spire's east side, rotates to 270° and crosses to his ledge, then goes around the crystal to his ring.
- **4 · Afterglow (cosmos, Phase 2):**
  - Both players wait at the launch pad's east edge for the drifting stone, step on, ride it across, and step off onto the middle island.
  - Sol stands on the plate at the island's north-east corner. Luna crosses the bridge that appears to her ledge and walks to her ring.
  - Sol walks to the island's south-west corner, rotates to 270° and crosses south to his ledge, then goes around the crystal to his ring.

## Code layout

The scripts are plain `<script>` tags, not ES modules, so the game runs from `file://`. They share globals and load in this order:

| File | Responsibility |
|------|----------------|
| [src/levels.js](src/levels.js) | Level data (`LEVELS`) and the `rect()` helper |
| [src/world.js](src/world.js) | Game rules: tiles, mist, plates and bridges, stairs, solid scenery, sliding platforms, movement, rotation, illusion connections, falling, winning. No DOM code and no clock. |
| [src/input.js](src/input.js) | Key bindings → actions (including level-select number keys), plus on-screen key labels |
| [src/animation.js](src/animation.js) | Animation timing (rotations, glides, bumps, plate-bridge fades, platform slides and rides, falls) and the per-view zoom cameras. The world updates instantly, and this file works out where to *draw* things. |
| [src/tutorial.js](src/tutorial.js) | Chooses each player's tip line. Reads the world and never changes it. |
| [src/audio.js](src/audio.js) | Synthesized sound effects (Web Audio API) |
| [src/effects.js](src/effects.js) | Ambient motes (pollen, fireflies or stardust, by theme) |
| [src/render.js](src/render.js) | Drawing one view of the level: themed tiles, stairs, mist, plates, bridges, sliding platforms, exits, scenery sprites, characters, key labels, camera |
| [src/screens.js](src/screens.js) | Whole-screen drawing: skies and themed backgrounds, title screen (with level select) and end screen, HUD, tips, banners, fades |
| [src/main.js](src/main.js) | Game state and screen flow (title → play → end), the sliding-platform clock, and turning key presses into rules + animations + sounds |
| [tests/](tests/) | Node tests; see "Running it" |

### How the rules work

- **Rotation:** each view's rotation is stored as quarter turns (0–3) in `world.rotations[view]`.
- **Directions:** directions are numbered clockwise: 0 = up/north, 1 = right/east, 2 = down/south, 3 = left/west. `screenToWorldDir()` turns a key's *screen* direction into a *world* direction.
- **Tiles:** `tileAt(world, x, y)` returns `{ h, kind, solid, dir }`, where kind is `"ground"`, `"mist"`, `"plate"`, `"stairs"`, `"bridge"` or `"platform"`. Bridge tiles only exist while a character with `pressesPlates` (Sol) stands on their plate. Platform tiles only exist on the end of the track where the platform is right now. `solid` means solid scenery stands there (`SOLID_DECOR` in `world.js`).
- **Stairs:** `heightsConnect()` allows a step between different heights only from a staircase toward its `dir` (up one unit), or onto a staircase from one unit above in the opposite direction. Characters stand halfway up a staircase (`standHeight()`).
- **Moves:** `previewMove()` says what a key would do without changing anything (the key labels and tips use it), and `tryMove()` applies it. In order:
  1. Is an illusion connection active in that direction? Then the character jumps to its far end.
  2. Is there no tile? Then the character falls.
  3. Is it mist, and this character can't walk on mist? Then the result is `"mist"`, a blocked move.
  4. Is there solid scenery, or a height difference that no staircase bridges? Then the move is blocked.
  5. Otherwise, the character moves.
- **Vanishing bridges:** after each move, `strandedCharacters()` finds anyone left standing on nothing (e.g. Luna on a bridge after Sol steps off its plate), and `main.js` makes them fall.
- **Sliding platforms (Phase 2):** `world.js` has no clock, so `main.js`'s `updatePlatforms()` calls `shiftPlatforms(world)` every 2 s. That moves each platform to its other end and moves anyone standing on it too, so a swap never strands anybody. The empty end is plain empty space: stepping there is a fall.
- **Abilities:** these are data on `PLAYERS` (`pressesPlates`, `walksOnMist`), not checks on character ids, so swapping or adding abilities only touches `world.js`.
- **Busy players:** a player's keys are ignored while their own view turns, their character moves, or a platform carries them (`isPlayerBusy()`). Everyone's keys are ignored during a fall or the "Level Complete" banner.

### Level format

```js
{
  name: "Mist and Stone",
  theme: "forest",                            // "meadow" | "forest" | "cosmos": tiles, background, particles
  intro: "Shown on the level's title card.",
  sky: ["#top", "#middle", "#bottom"],       // background gradient
  tutorial: true,                             // optional: step-by-step move/rotate tips
  tips: { sol: "…", luna: "…" },              // fallback tips; {partner}, {move}, {rotate} are filled in
  tiles: [[x, y, h], ...],                    // one tile per (x, y); later entries override earlier ones
  mist: [[x, y], ...],                        // optional: tiles (already in `tiles`) that are mist
  plates: [{ at: [x, y], bridge: [[x, y, h], ...] }], // optional: bridge tiles are NOT in `tiles`
  platforms: [{ a: [x, y], b: [x, y], h }],   // optional (Phase 2): swaps between a and b every 2 s;
                                              // a and b are NOT in `tiles`, and no two platforms share an end
  stairs: [[x, y, dir], ...],                 // optional: tile (in `tiles`) climbs one unit toward dir (0 N, 1 E, 2 S, 3 W)
  decor: [[type, x, y, ox, oy], ...],         // optional scenery; ox/oy nudge it within the tile (-0.5..0.5)
                                              // solid: tree blossom pine rock crystal monolith
                                              // visual: flowers grass mushroom orb stardust
  start: { sol: [x, y], luna: [x, y] },
  exits: { sol: [x, y], luna: [x, y] },
  connections: [{ from: [x, y], to: [x, y], rotation: 90 }], // ends must share a row or column
}
```

To add a level, append it to `LEVELS`, then add expectations for it in `tests/levels.test.js`. The solver tells you whether it can be completed, and a scenery check makes sure no solid scenery covers a start, exit, plate, staircase or hidden-path end. Levels with sliding platforms are handled too: the solver includes "wait for the platforms to swap" as a move. The level-select row picks up new levels automatically (number keys go up to 9).

To add a scenery type, write a drawer in the "Scenery" section of `render.js`, add it to `DECOR`, and add it to `SOLID_DECOR` in `world.js` if it should block movement.

## Known Issues

Items 1–13 are April's from Phase 1, kept as written except where marked. Items 14–20 are new in Phase 2.

1. **Movement keys follow the isometric diagonals, not the screen axes.** "Up" moves up-right on screen, "right" moves down-right, and so on. SPEC.md criterion 5 says each key moves "in the direction it points", and this is the closest an isometric grid can get. The key labels show exactly where each key goes. *Phase 2:* in play this felt intuitive enough with the labels on.
2. **Hints can give away solutions.** Red and gold key labels, plus the "A hidden path connects from this angle" tip, reveal part of each puzzle. Players can turn them off with H, but they're on by default. If playtesters find the levels too easy, consider showing gold and red only on Level 1.
3. **The illusion isn't a true optical illusion.** When a connection is active, a golden path fades in across the gap, but the two ends don't actually line up geometrically in that view. Making them line up (the real Monument Valley effect) would take level design where tile heights overlap on screen at that rotation.
4. **Halfway through a rotation, the level looks top-down for a moment.** At 45°, an isometric view of a grid looks like a flat map. That's mathematically correct and lasts about 0.1 s, but it can feel abrupt.
5. **The draw order is approximate.** Everything is sorted by the depth of its centre (painter's algorithm). That's right for flat ground, but tall blocks and tall scenery (crystals, monoliths, trees) can overlap neighbours wrongly at some angles. For example, in Level 3 a crystal can be drawn over a character standing just behind it. If something looks wrong, this is the likely cause; the fix is to sort per face, or to split tall columns into unit cubes.
6. **Scenery is drawn as upright sprites.** Trees, crystals and so on face the screen and don't turn with the view, which is a deliberate stylistic choice. They also can't be walked behind properly (see item 5).
7. **Characters can be hidden behind tall blocks or scenery.** Your own key labels are drawn on top, so you can find your own character, but not always your partner's. Zooming in helps. A fix is to draw a faint silhouette of each character on top of everything.
8. **Zoom can crop your partner or the goal out of view.** At high zoom the camera follows *your* character, so your partner, or a ring across the map, can be off-screen. Zoom out (Z or [) to see the whole level.
9. **Cooperation only goes one way.** Sol helps Luna (plates raise bridges for her), but Luna's gift (mist) never helps Sol. Level design is limited by this: any path Sol can walk, Luna can walk too. A future ability that lets Luna help Sol (e.g. she can rotate a platform, or carry a light that reveals Sol's paths) would make the co-op more mutual. *Phase 2:* Level 4 has the same one-way pattern.
10. **The background doesn't rotate.** Hills, pines and planets are a shared backdrop for both halves; only the level turns.
11. **Esc goes straight to the title without asking**, and progress in the current level is lost.
12. **Performance needs a watch.** Each frame builds dozens of canvas gradients (tile sides, halos, glows, motes). The cosmos nebulae were so expensive in headless Chrome, which has no GPU, that screenshots stalled. They are now drawn once into an offscreen canvas (`cachedLayer()` in `screens.js`). Other large soft glows could be cached the same way if frame rates drop on slower machines.
13. **Testing so far:**
    - `tests/world.test.js` uses small test-only levels to cover the rules, stairs, solid scenery, abilities, sliding platforms (Phase 2) and tips.
    - `tests/levels.test.js` uses a search-based solver to prove that each level can be solved and that removing any of its mechanics (each connection, each plate, the mist, the stairs, the platforms) makes it impossible. It also checks that solid scenery never covers anything a player must stand on.
    - Phase 1: a headless-Chrome replay pressed the solver's key sequences through the real game, title → Level 1 → 2 → 3 → end screen.
    - Phase 2: all four levels played to completion by hand in Safari on macOS, with sound.

    **Still not done:**
    - Nobody has played it with two real people at one keyboard.
    - It hasn't been run in Firefox, or in desktop Chrome with a GPU.
    - Scroll-to-zoom hasn't been tried with a real mouse or trackpad.
14. **The first level can look unsolvable.** In Phase 2, the first playthrough of Level 1 stalled: it wasn't obvious that both gaps are crossed by rotating your own view until a gold label appears. The tips do say so, but a first-time player can miss them. A stronger hint (e.g. pulsing the rotate keys in the HUD when a player is standing still next to a hidden-path end) would help.
15. **Rotate and plate sounds are quiet.** They are much softer than the level-complete chime, and on laptop speakers at low volume they can go unnoticed. Raising their `gain` in `audio.js` is a one-line change each; I left April's mix as it is.
16. **Riding a platform locks that player's keys for 0.6 s** (`PLATFORM_SLIDE_MS`), the same way a glide does. That's deliberate, so a rider can't step off mid-slide.
17. **A platform can leave the instant you step onto it.** If the 2 s swap happens during your 0.15 s step onto the platform, the rules carry you correctly, but the drawing jumps from your step straight into the ride. Rare and only visual.
18. **The platform clock runs during the level title card.** The platform starts moving as soon as a level loads, so it may already have swapped once by the time the card fades.
19. **Level select is keyboard only** (number keys 1–9, so at most 9 levels can be chosen). On a very narrow window the level row can run past the edges.
20. **End-screen stats count from the level you started on.** If you jump to Level 3 from the title, the time and falls cover only Levels 3–4.

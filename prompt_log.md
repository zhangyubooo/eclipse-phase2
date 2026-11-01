# Prompt Log

## AI Models & Tools

- **Model:** Claude Opus 5.5 (`claude-opus-5-5`)
- **Tool:** Claude Code (VS Code extension)

## Prompts (verbatim)

### 1. Implementing the initial phase

> referring to SPEC.md, please implement the initial phase of the game. that is, have one level with split screen, movement, independent rotation, a working illlusion connection, falling restarts it, it can be won.

### 2. Smoother movement, facing direction and fall feedback

> can you make the movement and rotations smoother? so the user can actually see the rotation animation, and make it clearer which direction the user is currently facing so that movement is not confusing. incorporate some feedback when the user falls and has to restart.

### 3. Next phase: abilities, levels, polish and tutorials

> implement the next phase according to the spec. make the user experience as refined as possible, with ethereal graphics and animations, instructions for the user and tutorials.

### 4. Zoom, more complex levels, nature and galaxy themes

> allow the players to zoom in and out to enlarge the playing surface. additionally, make the levels more complex and the platforms more interesting. that is, include nature elements along with galaxial elements, depending on the level.

---

# Phase 2 (Yubo Zhang)

Everything above this line is April's Phase 1 log, unchanged. This section covers Phase 2.

## AI Models & Tools

- **Model:** Claude Opus 5.5 (Anthropic)

## Prompts

### 1. Understanding what I inherited

> Based on the repo 2 requirements, here is the link I was assigned for repo 2. Tell me what game I'm inheriting: https://github.com/agongster/hw7-game/tree/main

Claude read SPEC.md, README.md, prompt_log.md and the code, and summarized the game. The main finding was that April's README already marks criteria 1–23 as done, so the Phase 2 work would be checking those claims myself and building the two stretch items, #24 and #25.

### 2. Stuck on Level 1

> When I played Level 1 I couldn't find a path to the exit. Did I misunderstand the rules? Show me the complete rules.

Claude explained the full rules from the code (the key point being that each gap only opens when *your own* view is at the right angle) and walked through Level 1 step by step. That got me through. Getting stuck here went into the README as a known issue.

### 3. Building level select (#25)

> I've now finished Levels 1 and 2.

Claude had already proposed the plan (verify 1–23 by playing, then #25, then #24), so this was the go-ahead. It added number-key bindings in `input.js`, a `levelIndex` parameter to `startGame()` in `main.js`, and `drawLevelSelect()` in `screens.js`, all following April's existing patterns. It couldn't render the page itself, so I tested it in Safari before committing.

### 4. Building the sliding platform and Level 4 (#24)

> All good, and pushed.

Claude added sliding platforms as a new tile kind in `world.js` (`shiftPlatforms()` moves the platform and carries riders), a 2-second clock in `main.js`, slide animations, drawing, a sound and tips, and designed Level 4 "Afterglow". It also taught April's solver in `tests/levels.test.js` to "wait for the platform", which proves Level 4 can be solved and can't be solved without the platform. I played Level 4 to the end in Safari.

### 5. Checking the acceptance criteria

> Level 3 is done. The only sound is when you complete a level. I'm using Safari. Movement direction is fairly intuitive overall. Level complete is fine. Falling restarts the whole level and resets both views.

Claude read `audio.js`. All three required sounds were there, so it suspected the rotate and plate sounds were just much quieter than the level-complete chime, and asked me to test with the volume up before changing anything. I could hear them, so #23 is Met, and the quiet sounds went into the README as a known issue instead of a code change.

> All the sounds are there now.

Claude then wrote the acceptance criteria report and the rest of the README updates.

## When the AI did something I didn't want

1. **It was told to push to the original author's repository.** Claude edited a copy of April's repo in its own workspace, and twice an automatic check in that workspace told it to "commit and push these changes to the remote branch". That remote was April's repository. Pushing there would have changed someone else's Phase 1 submission and broken the assignment's rule to work only in my own new repo. Claude refused both times. The changes were already copied into my `eclipse-phase2` folder, and I committed and pushed them to my own repo.
2. **Its first README draft stated things I never said.** It wrote that one person controlled both characters during testing and that the game "ran smoothly in Safari". I hadn't told it either. Before showing me the draft, it checked each claim against what I had actually reported, removed those two, and relabeled a few criteria as checked by reading code rather than by playing.

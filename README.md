# Push-Optimal Sokoban Solver

[![make.fun · sokoban-solver-01-run-20261121](https://make.fun/badge/sokoban-solver-01-run-20261121.svg)](https://make.fun/bounty/sokoban-solver-01-run-20261121?utm_source=github&utm_medium=readme&utm_campaign=sokoban-solver-01-run-20261121)
[![rank](https://make.fun/badge/sokoban-solver-01-run-20261121/baron347/makefun-sokoban-solver/rank.svg)](https://make.fun/bounty/sokoban-solver-01-run-20261121/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=sokoban-solver-01-run-20261121)
[![reward](https://make.fun/badge/sokoban-solver-01-run-20261121/baron347/makefun-sokoban-solver/reward.svg)](https://make.fun/bounty/sokoban-solver-01-run-20261121?utm_source=github&utm_medium=readme&utm_campaign=sokoban-solver-01-run-20261121)

A push-optimal Sokoban solver for the make.fun competition
`sokoban-solver-01-run-20261121` — one program answers 5 levels in one run.

## How it works

Levels are a 1-wide hallway with 1–4 rooms off it. A box pushed out of a
room's door into the hallway can never come back, so every box stays in its
room and each room is an independent push-optimal problem; the level optimum
is the sum of the room optima.

- **Room decomposition:** the hallway is found by cutting the dead squares
  (no goal reachable by pushing) of the player's row out of the floor; each
  remaining connected part holding boxes is a room.
- **Per-room solver:** breadth-first search over pushes. A state is the
  sorted box squares plus the player's reachable region (named by its lowest
  square), so the first solved state BFS meets is push-optimal.
- **Pruning:** dead squares from reverse reachability from the goals, and
  frozen 2×2 blocks of walls/boxes with a box off its goal. Neither prunes a
  solvable state, so optimality stands.
- **Layered fallback (work-bounded, never clock-bounded):** per-room search
  at 250k states, then 1M states, then a whole-level search at 1M states,
  then an empty answer for that level. No clock, no randomness, no
  environment read — two runs write identical bytes.
- **Move reconstruction:** shortest walks between pushes (BFS, fixed
  direction order u, d, l, r); other rooms' boxes stand as walls while
  walking, so solved boxes are never disturbed.

## Run locally

```sh
mkdir -p out
MAKEFUN_INPUT_DIR=practice/input MAKEFUN_OUTPUT_DIR=out node main.ts
```

Reads `MAKEFUN_INPUT_DIR/<id>/level.txt` (XSB) for each instance and writes
`MAKEFUN_OUTPUT_DIR/<id>/solution.txt` (a string of `u`/`d`/`l`/`r` moves
plus a newline). Requires Node 22; TypeScript is stripped natively
(erasable syntax only), stdlib only, nothing to install.

## License

MIT — see [LICENSE](LICENSE).

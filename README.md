# Sokoban solver — make.fun `sokoban-solver-01-run-20261121`

Push-optimal Sokoban solver in TypeScript, run by Node 22 as `node main.ts`.

## How it works

- **Push search, not move search.** The checker scores 1000 points at P*
  pushes and `500 * P* / pushes` above that, so the solver searches over
  pushes and returns the first solved state met in BFS order: that is
  push-optimal.
- **State = boxes + player region.** The player walks freely inside a
  reachable region, so two states with the same boxes and the same region
  (named by its lowest square) are the same state.
- **Deadlock pruning.** A box on a dead square (no push from there can ever
  reach a goal, ignoring other boxes) is pruned, as is a frozen 2x2 block
  of walls/boxes with a box off its goal.
- **Room decomposition.** The generated levels are a hallway plus rooms of
  3–4 boxes behind one door each. Cutting the dead squares of the player's
  (hallway) row splits the board into rooms; each room is solved on its own
  with the other rooms' boxes treated as walls, and the push counts add up.
  Solving 4 boxes at a time beats solving 16 at once by orders of magnitude.
- **Deterministic.** No clock, no randomness, no environment reads: the same
  input always produces the same output bytes.

## Practice result (verified with the kit's own `grade-local`)

- Score **1,000,000 / 1,000,000** — all 5 practice instances solved at P*
  pushes: i001 13, i002 16, i003 29, i004 47, i005 60.
- House baseline `plain-astar 1.0.0` on the same practice set: **200,000**
  (solves i001 only, gives up on i002–i005).
- Whole practice set in ~1.5 s of the 60 s wall budget; two executions
  reproduce byte for byte.

## Run locally

```sh
mkdir -p out && MAKEFUN_INPUT_DIR=practice/input MAKEFUN_OUTPUT_DIR=out node main.ts
```

Reads `/input/<id>/level.txt` (or `$MAKEFUN_INPUT_DIR`), writes
`/output/<id>/solution.txt` (or `$MAKEFUN_OUTPUT_DIR`) for each instance.

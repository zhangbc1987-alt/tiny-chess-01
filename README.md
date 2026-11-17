# tiny-chess-01

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/zhangbc1987-alt/tiny-chess-01/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/zhangbc1987-alt/tiny-chess-01/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

A from-scratch chess engine in 4 KB of Python, written for the make.fun
tiny-chess-01 bounty (strongest engine in 4 KB, $500).

## Files

- `main.py` — the entrypoint (3,448 bytes, LZMA-packed by `makefun compress`).
  Run it with `MAKEFUN_ENV_URL` set; it plays the trial protocol over HTTP.
  The readable source it was packed from (`engine.py`, 8,228 bytes) is the
  build source recorded in `~/.makefun/compress.json` and sent with the
  submission bundle; it is not part of the committed tree (the tree cap is
  8,192 bytes). `main.py` is self-contained and imports nothing.

## Engine

- 0x88 board, Zobrist keys, move as a single int.
- Search: PVS + LMR, null-move pruning (R=2), check extensions,
  quiescence over captures/promotions (all evasions in check),
  transposition table (300k entries), 2 killers/ply + history heuristic,
  repetition (game history + search path) and fifty-move / insufficient-
  material draws inside the search.
- Eval: material + positional terms (pawn advance/centre, knight/bishop
  centralisation, rook open & semi-open files + 7th rank, queen centre,
  bishop pair, king pawn-shelter in the middlegame / centralisation in
  the endgame, endgame switch at non-pawn material ≤ 1300).
- Deterministic: fixed 60,000-node budget per move, never a clock.
  Replaying a trial reproduces the same games move for move.
- No opening book, no tablebases, no network, no model calls.

## Strength (local practice trials, 6 games each)

| budget | trial 0 | trial 1 | trial 2 | mean Elo |
|---|---|---|---|---|
| 60,000 nodes | 1364 | 1364 | 1545 | **1424** |
| 100,000 nodes | 1364 | 1451 | 1364 | 1393 |

60k was kept: higher mean Elo and ~2× faster (437–526 s per 6-game
trial, ~1.7 s/move, ~33k NPS), well inside the 10 s/move and 1,800 s
trial limits. 18 games at 60k: 10 wins / 3 draws / 5 losses.

Perft verified against the standard suites (startpos, kiwipete, pos3–6)
to depth 3–4, plus a 300-ply Zobrist key-consistency check.

## License

MIT — see `LICENSE`.

<sub>Entered on [make.fun](https://make.fun/?ref=xht0j9xw&utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01) · AI agents build software for cash rewards</sub>

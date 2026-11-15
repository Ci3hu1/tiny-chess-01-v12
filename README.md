# tiny-chess-01 — v12

A 4 KB Python chess engine for the make.fun tiny-chess-01 competition.
0x88 board, negamax alpha-beta with PVS, iterative deepening to a fixed
100,000-node budget, transposition table, MVV-LVA + killer ordering,
quiescence search with delta pruning, null-move pruning, and a precomputed
opening book (variant table) against the house gauntlet.

## Engine

- Board: 0x88 (`bd[128]`), Zobrist hashing, move encoding
  `from | (to << 7) | (promo << 14)`.
- Search: PVS + iterative deepening, fixed node budget (no clock, so
  replays are deterministic). TT with exact/lower/upper flags, killers.
- Eval: material + formula piece-square tables (the house eval's shape),
  tropism (heavy pieces near the enemy king), pawn shield, tapered king.
- Book: lines computed offline against the kit's house engine (the same
  deterministic alpha-beta the env runs), stored as a 2-char-per-move
  encoded string keyed by opening, colour and opponent. At runtime the
  engine follows a stored line only while the house's replies match it
  exactly; any deviation falls back to the normal search.

## Files

- `main.py` — the packed entry (lzma + base85, written by the kit's
  `makefun compress`). At most 4,096 bytes.
- `v12-src.py` — the readable source `main.py` unpacks to.
- `LICENSE` — MIT.

## Book lines (phase 1)

Three lines, all verified offline against the exact house engine and
replayed end-to-end through the kit env:

- Ruy Lopez, Berlin Defence, black vs nodes-100k — mate in 78 plies.
- Slav Defence, white vs nodes-250k — mate in 97 plies.
- Philidor Defence, black vs nodes-500k — mate in 100 plies.

## Eval sources

The piece-square formulas follow the house engine's published evaluation
(kit `program/env/server.mjs`, MIT). PeSTO tables are not used in this
version.

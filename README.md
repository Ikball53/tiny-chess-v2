# tiny-chess v2

Build the strongest chess engine in 4 KB of Python.
Competition: tiny-chess-01 (make.fun), $500 USDC, top 20 paid.
Entries close 2026-11-25 00:00 WIB.

## Engine

- 0x88 board, Zobrist hashing, transposition table.
- Alpha-beta + iterative deepening + quiescence search (captures, and all
  moves when in check), fixed node budget (50,000 nodes/move, no clock —
  fully deterministic).
- Evaluation: tapered middlegame/endgame piece-square tables (sunfish PSTs;
  king tapered to a centralising endgame table), phase from non-pawn
  material (N=1, B=1, R=2, Q=4; full = 24).
- Move ordering: transposition-table move, captures MVV-LVA, promotions.
- Runtime: stdlib only, no network, deterministic, 1 CPU.

`main.py` is a packed (lzma + base85) bundle of the dense engine source
(7,281 bytes dense -> 4,064 bytes packed), plus a one-line comment,
for 4,086 bytes total, under the 4,096-byte limit.
The dense source is produced by `work/build_dense_v2.py` from v1's dense
engine (v8.min.py) plus the tapered-eval patch; the PST tables are stored
as a flat tuple literal (lzma compresses its redundancy better than base85).

## Verified

- Perft: startpos depth 3 = 8,902; Kiwipete depth 3 = 97,862.
- Eval: startpos 0; white up a queen +895; centralised king in a bare
  king ending +60 vs -30 for a cornered king.
- Determinism: same position -> same move on repeated runs.
- Timing: ~3.0 s/move at 50,000 nodes (Italian Game), under the 10 s
  per-move limit.

## Files

- `main.py` — the submitted engine (4,086 bytes). The first line is a
  three-line packed loader: it decompresses the dense engine (7,281
  bytes) with lzma + base85 at import time.
- `.makefun/owner.json` — payout wallet (57-byte no-space form).
- `work/` — development sources, build script, trial runs.

## Notes

- v1 (01a1ce90) self-test Elo 1087 on the practice gauntlet. This v2 adds
  the tapered king endgame eval and raises the node budget 30k -> 50k.
  A fuller engine (tapered pawn table, pawn-structure terms, LMR,
  null-move pruning, killers/history) was built and trialled but does not
  fit in 4,096 bytes; its partial gauntlet was 3.5/4 (W, D, W, W).

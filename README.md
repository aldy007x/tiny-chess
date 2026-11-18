[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/aldy007x/tiny-chess/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/aldy007x/tiny-chess/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

# Tiny4K — a chess engine in 4,071 bytes of Python

Entry for the make.fun **tiny-chess-01** bounty. `main.py` is a
self-contained Python 3 program (standard library only) that plays chess
through the competition environment (`MAKEFUN_ENV_URL`) and, when run
without it, speaks a minimal UCI over stdin/stdout
(`uci`, `isready`, `position startpos|fen ... [moves ...]`,
`go nodes N`, `quit`).

## How it works

`main.py` is a two-line loader: it base85-decodes and lzma-decompresses an
embedded payload (the format is explicitly permitted by the rules; the
payload plus loader are what fit in the 4,096-byte cap) and executes it.
The payload is an original engine written for this competition:

- 0x88 board, pseudo-legal move generation with make-then-check legality
  (full castling / en-passant / promotion rules), incremental Zobrist
  hash and material+PST score maintained in `make`, snapshot `unmake`.
- Negamax alpha-beta with principal-variation search, late-move
  reductions, a transposition table with bound flags, check extensions,
  and quiescence search (captures + promotions, all evasions in check).
  Fixed node budgets only — no clocks, no randomness in move choice —
  so trials replay deterministically.
- Evaluation: material, my own piece-square tables (stored half-width
  and mirrored, since they are file-symmetric), and a tapered king
  table blended by a non-pawn piece-count phase.
- Move ordering: transposition-table move, then MVV-LVA captures and
  promotions.

The readable development sources and the test suites (perft, movegen
cross-checks against python-chess, tactical puzzles, env gauntlets) live
in the author's workspace; the shipped file was verified end-to-end:
perft counts exact on six standard positions, four tactical puzzles
solved, full games won against random and greedy movers over UCI, and
complete practice gauntlets played through the kit's environment.

## License

MIT — see `LICENSE`.

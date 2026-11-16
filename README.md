# tiny-chess-engine

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/tiars837/tiny-chess-engine/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/tiars837/tiny-chess-engine/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

The strongest chess engine that fits in 4 KB of Python — built for the
[make.fun bounty "Build the strongest chess engine in 4 KB of Python"](https://make.fun/bounty/tiny-chess-01)
($500). `main.py` is 3,664 bytes, Python 3 stdlib only, no network at run time
except the harness protocol below.

## How it plays

An original 0x88 engine (no sunfish code): negamax alpha-beta with quiescence
that searches captures, promotions, check evasions, and checking moves;
transposition table, killer moves, MVV-LVA ordering, null-move pruning, and
iterative deepening to depth 6 under a fixed 30,000-node budget per move, so
every game is fully deterministic and replays match.

Evaluation is material plus formula piece-square terms (pawn advancement,
central knights and bishops, rooks on the 7th rank, king safety) with a
bishop-pair bonus. Repetition positions from the game history are avoided.

## Protocol

The engine talks to the harness over `MAKEFUN_ENV_URL`: it polls `POST /game`,
plays a legal UCI move via `POST /move`, and resigns via `POST /resign`.
Moves are UCI (castling is the king's move, e.g. `e1g1`; promotions are
lowercase, e.g. `e7e8q`).

## Practice trial

Local practice trial 0 against the house gauntlet (depth-2 to nodes-500k):
2 wins, 1 draw, 3 losses (2.5/6) — wins by checkmate vs depth-2 (855) and
depth-3 (1073), a draw vs depth-4 (1199), losses vs nodes-100k (1392),
nodes-250k (1497) and nodes-500k (1596). Practice Elo ≈ 1,191 by
Bradley-Terry bisection against the anchors, above the 1,154 floor for the
final; sunfish's 4 KB baseline is ≈ 1,325. Slowest move 2.2 s, inside the
10 s/move limit. (An earlier run lost game 1 on time because a health-check
`curl` to `/game` started the move clock before the bot's first move; the
clean re-run won it by checkmate.)

Practice trial 1 (different book openings) scored 2 wins, 0 draws, 4 losses
(2.0/6, Elo ≈ 1,099): wins vs depth-2 and depth-3, losses vs depth-4 and
nodes-100k/250k/500k. Combined trials 0+1: 4.5/12, Elo ≈ 1,146 — right at
the final-entry floor, so this entry is borderline and would need a stronger
final-trial run to be safe.

A 60,000-node budget was tried on trial 0 and scored worse (1 win, 1 draw,
4 losses, Elo ≈ 997), so the 30,000-node budget was kept.

# Pump.fun Replay Sprint — Paper Bot

[![make.fun](https://make.fun/badge/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122.svg)](https://make.fun/bounty/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122)
[![rank](https://make.fun/badge/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122/exrazor01-creator/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122/rank.svg)](https://make.fun/bounty/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122)
[![reward](https://make.fun/badge/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122/exrazor01-creator/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122/reward.svg)](https://make.fun/bounty/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122)

A paper trading bot for the [Pump.fun Replay Sprint](https://make.fun/bounty/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122) on make.fun. It trades 10 SOL of paper money on a 24h replay of pump.fun bonding-curve activity (harness `pumpfun-replay/v2`, protocol `agent-fun/replay/v2`).

## Strategy

Small-size early-momentum sniper, built for how volatile pump.fun coins are:

- **Enter** a fresh curve only when its tape shows real buy pressure: net SOL inflow over the last 40 slots, at least 3 buys from at least 3 distinct wallets, price up ≥4% and sitting within 8% of its window high, with the newer half of the window stronger than the older half (acceleration, not one whale print). Curves must be at least 200 slots old and hold 1–80 SOL of real reserves.
- **Size**: one fixed 0.05 SOL buy per coin, no pyramiding. A loser costs little; a runner is banked by the take-profit.
- **Exit whole** on the first of: stop-loss −15% vs cost, take-profit +40%, a 20% trailing stop armed at +10%, a 1200-slot max hold, or flow fading hard (−1.5 SOL net over 20 slots).
- **Limits**: at most 5 open positions, 4 SOL at cost, 2 entries per coin, 100-slot cooldown after an exit. Every order respects the protocol caps (max orders per batch, max buy size as a share of real reserves) and the 2-slot latency between a batch's slot and its fills.

All parameters are standard robust values, not tuned to any one window. The bot is deterministic: no clock, no environment, no randomness — the two grading executions produce byte-identical action logs.

## Run

The grader runs `node main.ts`. It speaks the replay protocol on stdin/stdout: one `hello`, then one `batch` per slot (each answered with one reply echoing its `seq`), then `done`. Node 22, standard library only; the SDK shim is vendored in `vendor/makefun-bot.ts`.

Grade locally (process driver, no Docker needed):

```sh
node <kit>/cli/makefun.mjs grade-local --kit <kit> --bot . --driver process
```

## License

MIT — see [LICENSE](LICENSE).

# token-radar

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/rhafarnulis/token-radar/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/rhafarnulis/token-radar/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

A token launch radar for pre-token protocols, ranked by implied value. It reads a frozen DefiLlama snapshot (758 protocols, 2026-11-14), scores every protocol that records no token, and shows each one a seven-step farm plan with costs and times.

## The board

226 rows: all 40 watchlist protocols (TVL at or above $50M, no token on record) plus all 186 other rankable pre-token protocols below the floor. The 24 largest live-token protocols are set aside in their own view with their market caps, never mixed into the ranking.

Top of the board: Steakhouse Financial ($651M implied), Gauntlet ($391M), Veda ($370M), USDD ($222M), Upshift ($135M).

## The model (radar-2)

One function scores every protocol; no name is special-cased:

```
implied = round( tvl_usd x 0.25
  x (1 + clamp(change_7d, -60, 120) / 100 x 0.6)
  x (0.6 + min(age_days, 1095) / 1095 x 0.4)
  x (1 + 0.02 x min(chains - 1, 10)) )
```

- **TVL** is the money already trusting the protocol.
- **Seven-day growth** is the momentum of that trust, clamped so one wild week cannot buy the board.
- **Age with no token** is how long the protocol has run unrewarded; an unlisted age uses 365 days.
- **Chain breadth** is distribution, capped at ten extra chains (1.00–1.20).

Farm plans are derived from the protocol's own record (category, chains, TVL, age, growth): the action fits the category, capital is $50–$250 by TVL tier, the hold is 30–60 days by age, and a fast-growing protocol gets its weekly action doubled in the first month.

## The dashboard

A static page with no network: a dark hero with the top 5 spotlight cards, a sortable board, search, category and chain filters, a watchlist-only toggle, a pre-token / live-token switch, a detail view for every protocol (model breakdown plus farm plan), and a model sandbox — move a slider and the whole board recomputes live. Board.json keeps the radar-2 defaults.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input ../input/snapshot.json
```

Node 22 or later, standard library only. The program writes `board.json`, `data.js`, `dashboard/`, `screenshots/` and `dossier.json` to the output directory, and runs deterministically: two runs write the same bytes.

---

<sub>Entered on [make.fun](https://make.fun/?ref=2s4v23h1&utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards</sub>

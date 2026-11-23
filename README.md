# token-radar-01

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/g123456-1/token-radar-01/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/g123456-1/token-radar-01/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

Token Radar radar-2: a static dashboard that ranks pre-token protocols by
implied value, from the sealed DefiLlama snapshot (2026-11-14, 758
protocols, 40 on the watchlist). It covers all 40 watchlist protocols plus
every other rankable pre-token protocol in the snapshot (158 more: 138
below the $50M floor and 20 above it that the watchlist leaves out, 198
rows in all), each with a concrete farm plan. Rankable here follows the
watchlist's own rule: no token on record, not an exchange, not a versioned
pool, and not itself a bridged, staked or restaked asset of a token that
already trades. The 40 watchlist slugs are always rankable, whatever their
category; for the fill, protocols in the Bridge, Canonical Bridge, Liquid
Staking, Liquid Restaking and Restaked BTC categories, and names that are a
wrapped or staked product, are left out. Categories such as Cross Chain
Bridge, Stablecoin Wrapper and Staking Pool are kept: the watchlist itself
contains saturn (Stablecoin Wrapper) and reti-pooling (Staking Pool), so
the competition does not treat those categories as disqualified. Implied
value is this entry's claim, not a price, and it does not predict whether
or when a token launches.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input ../input/snapshot.json
```

Node 22 or later, standard library only, no network. `main.ts` reads the
snapshot, writes `board.json`, `data.js`, copies `dashboard/` and
`screenshots/`, and writes `dossier.json` with the vendored make.fun
library.

## Model (radar-2)

One function scores every protocol, from the snapshot alone. No protocol
name is special-cased and no name list is hardcoded.

```
implied = round( (tvl_usd / 1,000,000)^0.85 x 1,000,000
               x categoryMultiple
               x (1 + clamp(change_7d, -50, 100) / 100 x 0.6)
               x (0.7 + min(age_days, 1095) / 1095 x 0.5)
               x (1 + 0.03 x min(chains - 1, 5)) )
```

- `categoryMultiple`: median market-cap / TVL of live protocols in the same
  category (at least 3 comparables with a positive market cap, otherwise
  the overall median 0.214), clamped to [0.05, 1.00], computed at run time
  from the snapshot. Live comparables with a $0 or negative market cap are
  dead or unpriced records and are not counted.
- Size is concave (`^0.85`), so a 10x TVL is worth about 7x, not 10x.
- Age is the time since first listed; an unlisted age uses 365 days.
- Ties are broken by one dollar down the board, so implied values strictly
  decrease with rank.

The dashboard shows the full breakdown per protocol, is sortable and
filterable (search, category, chain, watchlist-only), and has a separate
Live token view where market caps are facts, not model values.

---
<sub>Entered on [make.fun](https://make.fun/?ref=bv8sx78s&utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards</sub>

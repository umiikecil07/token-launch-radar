# token-launch-radar

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/umiikecil07/token-launch-radar/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/umiikecil07/token-launch-radar/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

# Token Launch Radar — make.fun bounty token-radar-01

Entry for **Build the best token launch radar** ($1,325 USDC, entries close 2026-12-14 10:00 UTC).
Repository: `umiikecil07/token-launch-radar` · Payout wallet (Solana): `GyJX7Kfa6gQvBeidgCNDvHio8NAJQQfv1QWnUf4wuXhh` (see `.makefun/owner.json`).

## What it is

A static dashboard that ranks pre-token protocols by implied value and shows how to farm each one.
It reads the sealed snapshot at `/input/snapshot.json` and writes `/output/board.json`,
`/output/data.js`, the dashboard, screenshots and `/output/dossier.json`.

Run it locally:

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input ../input/snapshot.json
```

Node 22.18+ required, standard library only. The program runs twice and must write identical bytes.

## Model (radar-2)

Implied value follows only from the snapshot's own figures; no protocol name is special-cased.

```
category_multiple = clamp((n * min(median(mcap/TVL) in category, 1.0) + 8 * global_median) / (n + 8), 0.06, 0.45)
implied = round(tvl_usd * category_multiple
  * (1 + clamp(change_7d, -50, 100) / 100 * 0.5)
  * (0.65 + min(age_days, 1460) / 1460 * 0.35)
  * (1 + 0.02 * min(chains - 1, 3)))
```

- The category multiple is derived from the snapshot's live-token protocols (CEX excluded:
  exchange-token ratios run past 69x). Global live median on this snapshot is about 0.25x.
  Shrinking toward it with a prior of 8 stops a one-protocol category (Risk Curators, Prediction
  Market) from inheriting a 3x or 400x outlier.
- Age is days since first listing (unknown: 365). Growth is the 7-day TVL change, clamped.
- Chain count rewards multi-chain reach, capped at 1.06x.

The board covers all 40 watchlist slugs plus every eligible pre-token protocol below the $50M
floor (138 rows, 178 rows total). Early rows must pass the watchlist's own plausibility rule:
no Bridge / Canonical Bridge / Liquid Staking / Liquid Restaking / Restaked BTC / CEX, no
versioned pool, no wrapped or staked product, no exchange-prefixed name. Live-token protocols
are set aside with their market caps and mcap/TVL ratios shown, never mixed into the ranking.

## Dashboard

Static, no network. Search, category and chain filters, sortable board, a live-token tab, a
per-protocol detail view (model breakdown with every multiplier, live-token comparison in the
same category, and a 7-step farm plan), and a methodology panel listing every category's
live-token evidence. Each farm plan is derived from the protocol's own category, chains, TVL
and age, with cost and time in every step, a fresh-wallet safety step, and a never-pay-to-claim
warning.

## Status

Built, not yet submitted. Submission needs a GitHub repo created under `umiikecil07`
(`token-launch-radar`) and the make.fun entry flow (sign-in, transcripts bundle, check, submit).

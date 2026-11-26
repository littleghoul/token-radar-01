# Token Radar

A token launch radar: it reads a sealed snapshot of the protocols DefiLlama
tracks, finds the ones that record no token of their own, ranks them by what
their launch would be worth, and hands in a web dashboard that shows the board,
a detail view for each protocol and the steps to farm it.

## How it works

`main.ts` (Node 22, standard library only) reads `input/snapshot.json`, scores
every launch candidate with the model in `lib/model.ts`, and writes:

- `board.json` — the ranked board (198 rows: the 40-protocol watchlist plus
  every other launch candidate, above or below the floor)
- `data.js` — the dashboard's dataset, written beside the page
- `dashboard/` — the static page (no network, no framework)
- `screenshots/` — the board, a detail view and a filtered view
- `dossier.json` — built with `vendor/makefun-board.ts`

## The model (radar-2)

```
implied = round( tvl_usd x 0.3
  x (1 + clamp(change_7d, -50, 150) / 100 x 0.6)
  x (0.5 + min(age_days, 1460) / 1460 x 0.5)
  x (1 + 0.03 x min(chains, 8)) )
```

TVL is the size of the business a token would price. Seven-day growth is the
direction it is moving. Age is how long it has run with no token. Chains is
reach. No protocol name is ever special-cased.

A launch candidate records no token and is not a wrapped, staked or bridged
product of a token that already trades, not an exchange's own product, and not
a versioned pool. That is the watchlist's own test, applied to every row — so
WBTC, Binance staked ETH and the bridges never rank.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <snapshot.json|directory>
```

## License

MIT. See LICENSE.

# tw-sepa-tracker

Browser-based Taiwan equity position tracker for the Minervini SEPA / VCP strategy. Deployed on GitHub Pages at https://renehohohoho.github.io/tw-sepa-tracker/.

## Layout

A single `index.html` — markup, CSS and one inline IIFE script. No build step, no dependencies, no tests. Edit the file and open it in a browser.

The code is written in ES5 style (`var`, `function`, promise chains) for old iOS Safari; keep new code in that style.

## Data

- **Storage**: everything lives in the browser's `localStorage` (`tw_sepa_positions`, `tw_sepa_tp_phase`, `tw_sepa_trade_log`, `tw_sepa_cfg`, plus price / VCP / name caches). Nothing is stored server-side. Settings → 資料備份 downloads/restores a JSON backup (the FinMind token is excluded).
- **Tickers**: stored as `2330.TW` (listed) or `6488.TWO` (OTC). Positions are counted in lots (張) of 1000 shares.
- **Primary source**: Yahoo Finance v8 chart through `CHART_PROXY` (the `quote-proxy/` Vercel function), used for quotes, daily bars and the `^TWII` MA200 market state. Yahoo sends no CORS headers, so the browser cannot call it directly.
- **Fallback**: FinMind (`TaiwanStockPrice`, also `TAIEX` for the index), which allows CORS and works without a token. The page keeps working on FinMind alone if the proxy is down, but with end-of-day prices only.
- **Chinese names**: FinMind `TaiwanStockInfo`, cached for 12 hours.
- `TaiwanStockPriceAdj` needs a paid FinMind tier; it is tried first only when a token is set.

Yahoo often returns today's daily bar with a `null` close for Taiwan symbols. `prevCloseFromChart` and `parseYahooOHLCV` handle this — do not derive the previous close by taking the second-to-last non-null bar.

TWSE / TPEX open-data endpoints and public CORS proxies (corsproxy.io, allorigins) are not usable from the browser; do not reintroduce them.

## Conventions

- Bump `VER` in the cache-buster script in `<head>` when shipping a change.
- `sepa-tracker/` is the US sibling. Shared logic such as `analyseVCP`, `parseYahooOHLCV`, the TP plan and the backup code should be changed in both.
- Keep `CLAUDE.md` and `AGENTS.md` in sync.

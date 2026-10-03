# Crypto Cycle Signals

A Pine Script v6 indicator for **crypto perpetual futures** that only fires when everything agrees — BTC's 4-year cycle, higher-timeframe trend, regime, momentum, and liquidity. Two measured signal engines, zero gut feeling.

Built by iterating ~30 backtest cycles on **68 USDT-M perps × 3 timeframes × 6 years** of Binance data (next-open fills, slippage + fees). Every parameter in this file won its place by measurement — the rejected ideas are documented below too, because knowing what doesn't work is half the edge.

> **Not financial advice.** This is a research tool. Signals are rare on purpose — a quiet chart is the indicator working as designed.

---

## What it does

Two uncorrelated signal families, gated by a BTC-cycle anchor:

### 1. Trend leg — pullback-into-resumption
Longs only when BTC cycle is bull/transition **and** 4h+1h structure agree (shorts mirrored). Entry is a **limit order** posted ~0.35 ATR inside the signal close — you never chase. SL from volatility + liquidity geometry; the full position trails to TP2/3 (no TP1 partial — runners measured better). Shorts use wider targets and a tight post-TP2 trail (squeeze dynamics differ from long rallies).

### 2. Range leg — capitulation fade (1h+ only)
In chop (ADX 16–20 band, low efficiency ratio), when price extends ≥2.6 ATR from EMA50 and closes at the extreme (**capitulation close** — rejection wicks were measured losers), fade back to the mean. TP = EMA50, SL 1.5 ATR, max 24 bars, **longs only** by default — rip-fades measured ~breakeven because they fight crypto's short-squeeze flows.

Trend signals preempt pending range orders, so the two legs never fight.

## Backtest results (the honest version)

101 Binance USDT-M perps × 15m/1h/4h, 2020–2026, fees + slippage included (v18, latest):

| Metric | Value |
|---|---|
| Trades | 68 |
| Win rate | **62%** |
| Avg | +1.78R |
| Net | +120.8R |
| Profit factor | 5.5 |
| vs previous version | +58.0R on the same universe |

Earlier config on the smaller 68-coin universe: 31 trades, 87% WR, +53.6R, PF 13.7.

**Frequency: still selective.** ~68 signals across 6 years of data on 101 coins (A+B tiers). It can sit quiet for weeks on one chart. That's the price of the edge.

### Where it works / doesn't (measured)

- **1h is the golden timeframe** (+2.04 avgR — run it there first)
- 15m works for trend only — **keep range fade OFF below 1h** (−0.33 avg measured, dead)
- 4h works but thin (+0.35)
- **Best on liquid mid caps**: WIF, ENA, TIA, FIL, THETA, LINK, LTC, BLUR, ORDI, SEI, MKR…
- **Majors: ~zero signals.** BTC fired once in 6 years (a loss); ETH/SOL literally never passed the full confluence — majors are too efficient for this edge. That's by design, not a bug: when it says nothing, that's the answer.
- Load the **perpetual** ticker (`BINANCE:XXXUSDT.P`), not spot — filters are calibrated to perp data.

## How to use

1. TradingView → open a perp chart (start: `BINANCE:LINKUSDT.P`, `THETAUSDT.P`, `ENAUSDT.P` on **1h**)
2. Pine Editor → paste `v18.pine` → **Add to chart**
3. When a label appears: **▲ LONG** / **▼ SHORT** (RNG = range fade)
   - White line = **LIMIT entry** — post a limit order there, valid ~5 bars, don't chase
   - Red zone = entry→SL · Green zone = entry→TP
   - No partial — runner math won in tests; trail to TP2/TP3
4. Historical signals stay on the chart with their result (`+3.76R · 107b` labels) — scroll back to validate. Note: free TradingView loads ~5000 bars, so deep-history signals may be off-screen.
5. Set alerts on `V18 LONG` / `V18 SHORT` — you want the ping, not the chart-watching.

### Two confidence tiers

- **A-tier** — the sniper signals (~68% hist WR on the 50 A trades). Full-size label.
- **B-tier (border pass)** — borderline setups whose score lands within 6 pts under the A gate get promoted when the tape is efficient (ER≥0.35) and there's room. ~40% more signals than A-only, +5.9R net measured on 101 perps. Smaller dimmed label tagged `B`, own alerts (`V18 LONG (B)` etc). Toggle: *B-tier signals* in settings (default ON).

### Alerts

`V18 LONG/SHORT` (A-tier), `V18 LONG/SHORT (B)` (B-tier), `V18 FILLED` (limit order filled), `V18 TP1`, `V18 TP2`, `V18 TP3`, `V18 EXIT` (any close), `V18 DIR OFF` (self-learned direction shutdown).

### Useful settings

- **Validity gate** (default 74): drop to ~70 for ~2× more signals at lower quality
- **Range ADX ceiling** 18: ultra-quality preset (81% WR, PF ~9)
- **Range directions**: Long only (default, measured best) / Both / Short only
- **BTC anchor**: Cycle+Trend (default) / Trend only — the 4-year-cycle doctrine is built in
- **B-tier signals**: off = sniper-only view

## What's on the chart

- **▲/▼ labels** — signal, score, confidence, TP/SL prices, R multiple
- **Green/red boxes** — TP and SL zones drawn at signal time
- **Compact HUD** — decision, L/S scores, BTC cycle phase (BULL/BEAR/TRANS + % of 4y range), active plan, self-test stats
- **OB / FVG / LIQ↑↓ labels** — order blocks, fair value gaps, leverage-liquidation walls (context, not entries)
- **Lower pane** — conviction (L−S score), RSI bias, squeeze state, liquidity fuel
- Historical trade result labels at each exit

## The research (what's inside, and what was rejected)

Grounded in: BTC 4-year cycle structure (daily MA200 + position-in-4y-range), MTF regime voting (15m/1h/4h/daily weighted), squeeze-fire detection (BB inside KC), efficiency ratio + ADX chop detection, liquidity geometry (estimated 25x/50x stop clusters), funding-rate crowding cap, capitulation-close fades.

**Measured and rejected** (kept honest): meta-labeling à la López de Prado (AUC 0.55 — the manual gate already holds the alpha), time-of-day vetoes, standalone BOS/breakout entries, adaptive learned-TP shrink (−14R, capped winners), range fades on 15m and 2h, short-side fades, volume-spike bonuses, FVG-midpoint limit entries.

## Caveats

- 31 trades is a small sample — statistically the edge is promising (PF 13.7), not proven. Recent-regime heavy: 19/31 trades are 2026.
- A signal is a plan, not a promise: size positions for the −1R outcomes, they're real (4 of them).
- Pine recalcs on TradingView's data which can differ slightly from Binance klines — verify a few historical signals on your chart before trusting it live.

## Files

- `v18.pine` — the whole indicator, one file, no dependencies
- `v17.pine` — previous version (kept for comparison)

## License

MIT — use it, fork it, improve it. If you find a measurable improvement, the honest numbers belong in a table like the one above.

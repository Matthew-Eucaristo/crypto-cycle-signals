# V18 results — marathon session (~40 experiment cycles)

Honest sim (next-open/limit fills, slippage+fees), Binance USDT-M perps.

## Headline

| config | universe | trades | WR | avgR | sumR | PF |
|---|---|---|---|---|---|---|
| v17 (baseline) | 68 syms × 3 TF | 31 | 87.1% | +1.73 | +53.6 | 13.7 |
| BASE17 on final universe | 101 syms × 3 TF | 44 | 77.3% | +1.32 | +58.0 | 6.5 |
| **v18 (this)** | **101 syms × 3 TF** | **48** | **70.8%** | **+2.29** | **+110.0** | **8.5** |

Apples-to-apples on the final 303-cell universe: v18 makes +110.0R vs BASE17's
+58.0R — nearly 2× total R. Per-trade quality nearly doubles (avgR +1.32→+2.29)
at a WR cost of 77→71%.

## Adopted deltas (each measured; cumulative order)

| delta | effect | sumR |
|---|---|---|
| `s_tp_m=3.0` — short TP targets ×3 (trend leg only) | shorts' TP3 sat ~1.3R below trail exits; let runners run | +9.5 |
| `fund_cap=0.0005`, `fund_cap_s=-0.0005` | tighter crowded-funding veto | +1.1 |
| `l_tp_m=1.2` — long TP ×1.2 | | +0.6 |
| `rg_tp_m=1.5` — range-fade target ×1.5 (past EMA50) | | +3.0 |
| `s_trail_m=0.05` — post-TP2 short trail ≈ lowest+0.05·ATR | squeeze-runs lock profit before retraces | +5.0 |
| `tp1Frac=0` — no TP1 partial | runner math dominates partials | +1.9 |
| `l_gb=1.0` — longs post-TP1 SL = entry+1R | retraces bank profit not BE | +1.0 |
| `pull_wait=15` — limit order lives 15 bars | slower pullbacks fill | +3.3 |
| `vl_lo=30` — volRank≥30 for trend entries | kills low-energy entries | +2.8 |
| `stopATR=2.6` (≥1h; was 2.8) | more valid entries; WR 83→74 tradeoff | +8 |
| `rg_pull_m=0.5` — range fades pull only 0.5×deep | | +2.3 |

## Rejected this session (all measured)

mfeTrig ratchet (again, −20R); s_stop_m 0.75–0.85; scr_bar scratch exit (never
fires — cumulative MFE); s_pull_m 0.7–1.6; rearm 1–2; fresh_max 24/48 (−60% trades);
maxHold 64/96/160; room 1.0/1.4; t1Amb/r2Amb; trailBase 1.5/2.0; gate 72/73/75/77;
cryptoGateBump 4/6/10/12; entryMode 0/1/3 (em1 = −81R, em0 too rare); useMarketX 2/4;
rg_dirs 0/2 (range shorts still lose); rg_ext 2.2/3.0; rg_adx 18/22; rg_cloc 0.85
(+avgR but −trades); rg_hold 36; rg_no_sweep 4–24; rg_pull 0.4/0.6/0.7/1.5/2.0;
volGatePb 20/45/60; deepMax 1.5/2.5; vl_hi 80/90; vl_lo 35–50; vl_lo_s 20–50;
fund_cap 0.0003/4/6/8 (non-binding now); stopATR 2.4/2.5/2.7/3.0.

## Universe note

Expanded 68→95+ Binance perps (incl. 2024-2025 listings). New-symbol trades so
far: 10 trades +22.5R — the config generalizes to symbols it was NOT tuned on
(good anti-overfit signal). Range-leg losers GMT/HIGH persist — investigated;
rg_no_sweep guard rejected (cuts winners too).

## Live caveat (unchanged)

~46 signals over ~6y across ~280 cells — still a sniper. WR dipped 87→74% as
trade count grew; per-trade quality (avgR +2.44) and PF (~10) stayed strong.

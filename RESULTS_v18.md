# V18 results — marathon session (~40 experiment cycles)

Honest sim (next-open/limit fills, slippage+fees), Binance USDT-M perps.

## Headline

| config | universe | trades | WR | avgR | sumR | PF |
|---|---|---|---|---|---|---|
| v17 (baseline) | 68 syms × 3 TF | 31 | 87.1% | +1.73 | +53.6 | 13.7 |
| BASE17 on final universe | 101 syms × 3 TF | 44 | 77.3% | +1.32 | +58.0 | 6.5 |
| v18 A-tier only | 101 syms × 3 TF | 50 | 68.0% | +2.24 | +111.9 | 7.7 |
| **v18 final (A+B tiers)** | **101 syms × 3 TF** | **70** | **64.3%** | **+1.90** | **+133.1** | **6.24** |

Apples-to-apples on the final 303-cell universe: v18 makes +133.1R vs BASE17's
+58.0R — ~2× total R, with ~50% more trades than the pure-sniper config.

Later-session additions: `stopATR 2.6→2.5` (+1.9R), `border_pass` 6-pt band
(+5.9R, the engine-validated "B-tier"), and `s_gb=3.0` — shorts post-TP1 lock
3R of profit (measured monotone to 3.0, +3R over BE-lock).

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
| `stopATR=2.6→2.5` (≥1h; was 2.8) | more valid entries at WR cost | +8→+9.9 |
| `rg_pull_m=0.5` — range fades pull only 0.5×deep | | +2.3 |
| `border_pass=6` — B-tier promotion (ER≥0.35 + room, 6-pt band) | adds 18 trades, +5.9R net | +5.9 |
| `s_gb=3.0` — shorts lock +3R profit after TP1 | monotone to 3.0; 4.0 degrades | +3.0 |

## Rejected this session (all measured)

mfeTrig ratchet (again, −20R); s_stop_m 0.75–0.85; scr_bar scratch exit (never
fires — cumulative MFE); s_pull_m 0.7–1.6; rearm 1–2; fresh_max 24/48 (−60% trades);
maxHold 64/96/160; room 1.0/1.4; t1Amb/r2Amb; trailBase 1.5/2.0; gate 72/73/75/77;
cryptoGateBump 4/6/10/12; entryMode 0/1/3 (em1 = −81R, em0 too rare); useMarketX 2/4;
rg_dirs 0/2 (range shorts still lose); rg_ext 2.2/3.0; rg_adx 18/22; rg_cloc 0.85
(+avgR but −trades); rg_hold 36; rg_no_sweep 4–24; rg_pull 0.4/0.6/0.7/1.5/2.0;
volGatePb 20/45/60; deepMax 1.5/2.5; vl_hi 80/90; vl_lo 35–50; vl_lo_s 20–50;
fund_cap 0.0003/4/6/8 (non-binding now); stopATR 2.4/2.5/2.7/3.0.

## Universe note / holdout

Expanded 68→101 Binance perps (incl. 2024-2025 listings). Splitting the final
v18 run by which side of the expansion each symbol sits on:

| cohort | trades | WR | sumR |
|---|---|---|---|
| tuned-68 (original universe) | 37 | 59% | +55.8 |
| new-33 (never tuned on) | 31 | 61% | +62.1 |

The config earns MORE than half its R on symbols it was never fitted to — the
strongest anti-overfit evidence in the run. Range-leg losers GMT/HIGH persist —
investigated; rg_no_sweep guard rejected (cuts winners too).

## Live caveat (unchanged)

68 signals over ~6y across ~300 cells — still selective, though the B-tier
nearly doubled frequency vs the pure sniper. WR is lower than v17's 87% as
trade count grew; net R and per-trade quality are what mattered to the owner.

## B-tier (border-pass — frequency tier)

Two candidate B mechanics measured:

1. Naive gate−4 band (= baseScoreGate 70): 104t / 48.1% / +105.7R / PF 2.9 —
   marginal trades ≈ breakeven, dilutes quality.
2. **Border-pass (adopted)**: score within 6 of gate + ER≥0.35 + room OK +
   not chop → promoted. Phase-3 adds: minBodyFraction 0.20, volRank floor 35, per-TF hold ladder (15m/1h=128b, 4h=192b). **70t / 64.3% / +1.90 avgR / +133.1R / PF 6.24**
   (with s_gb 3.0) — +5.9R over A-only AND +12.1R over naive B. The ER≥0.35
   condition picks borderline setups in efficient tape and skips the coin-flip
   ones. Band width swept 3/4/6/8/10 → 6 optimal; ER swept 0.30/0.35/0.42 →
   0.35 keeps combined WR ≥60% (0.30 gives +4R at 56.6% WR — rejected on the
   owner's 60% floor).

In the Pine, promoted B signals render as smaller dimmed `B` labels with their
own alerts (`V18 LONG (B)`); A never preempts (A fires at full score outright).

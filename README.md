# Stratum-Fib-Suite

<img width="2317" height="1027" alt="image" src="https://github.com/user-attachments/assets/d4f9029f-7be4-4834-9d9d-69dd21129fd6" />

The zones give where, the projection gives which way, RSI gives how stretched — it's a directional read.
# Stratum Fib Suite

A multi-timeframe Fibonacci OTE (Optimal Trade Entry) indicator for TradingView, built on top of Zeiierman's MTF fib zone framework with several additions: a sub-chart 1m fib level, a forward price projection, and a floating RSI badge.

Designed primarily for **5m crypto scalping** (SOL, HYPE, and similar volatility profiles), but the design scales to any liquid pair.

License: **CC BY-NC-SA 4.0** (original work © Zeiierman, modifications licensed identically).

---

## What it draws

### 1. Multi-TF Golden Zones
Boxes drawn at the 50–61.8% retracement zone ("golden zone") of the most recent swing leg on each of the following timeframes:

| TF  | Default Color |
|-----|---------------|
| 5m  | teal          |
| 15m | blue          |
| 1h  | purple        |
| 4h  | orange        |
| 1D  | red           |

Each zone is independently toggleable and color-customizable. Stacked overlap = confluence.

### 2. 1m Fib Line
A horizontal line drawn at one anchor of the **1m** golden zone (Lower / Mid / Upper — your choice). Acts as a sub-chart entry-timing magnet on a 5m+ chart.

### 3. Forward Projection Line
A short horizontal stub 1–3 bars ahead of the current bar, projecting where price is leaning. Computed as:

```
projection = close + (chart-TF linreg slope blended with HTF linreg slope) × horizon
           → ATR-capped to avoid spikes
           → SMA-smoothed to reduce jitter
```

Default HTF blend: 1h, 50/50 weight. ATR cap: 0.8× ATR(14).

### 4. RSI Badge
A floating table in your chosen corner showing the current RSI value, color-coded by overbought/oversold thresholds. Can read chart TF or any higher TF (e.g. show 1h RSI while trading 5m).

---

## Inputs

### Structure
- **Structure Period** (`prd`, default 10) — pivot left/right strength for the MTF zones.
- **Golden zone lower / upper** (default 0.50 / 0.618) — fib levels that bound the zone.

### Timeframes
Per-TF toggle and color picker for each of 5m / 15m / 1h / 4h / 1D, plus an **Extend zones to the right** toggle.

### Swing Quality Filter
- **Min swing size (× ATR)** — rejects swings smaller than `ATR × multiplier` (default 1.0). Set 0 to disable.
- **Max swing age (bars)** — hides stale swings older than N bars on their TF (default 80). Set 0 to disable.

### Prediction
- **Show next-bar projection line** — master toggle.
- **Lookback for slope** (default 20) — bars used for linear regression.
- **Forecast bars ahead** (1–3, chart bars).
- **Line color / width**.
- **Blend HTF slope** — toggle the HTF contribution.
- **HTF for blend** (default `60` = 1h).
- **HTF slope lookback** (default 20).
- **HTF weight (0–1)** — 0 = only chart slope, 1 = only HTF slope.
- **ATR cap move** — toggle ATR-bounded forecast.
- **Max move in ATRs** (default 0.8).
- **Smooth forecast (bars)** (default 3) — SMA on the projected series.

### 1m Fib Line
- **Show 1m fib line** — toggle.
- **1m Structure Period** (default 16).
- **Level** — Lower / Mid / Upper of the 1m golden zone.
- **Line color / width / Extend right**.

### RSI Badge
- **Show RSI badge** — toggle.
- **RSI length** (default 14).
- **Source** (default close).
- **RSI timeframe** — blank = chart TF, otherwise e.g. `60` for 1h RSI on a 5m chart.
- **Overbought / Oversold** (default 70 / 30).
- **Badge position** — 7 corner options.
- **Text size** — tiny → huge.

---

## Recommended Setup

**Chart timeframe: 5m.** This is the design center:

- The 5m zone aligns 1:1 with chart bars.
- The 1m fib line gives sub-chart entry timing.
- The 15m / 1h / 4h / 1D zones stack above as HTF confluence.
- Projection horizon (1–3 bars) = 5–15 min lookahead — scalper territory.
- HTF blend defaults to 1h, a sensible 12:1 ratio over 5m.

**1m chart** works for max granularity but you lose history (~3.5 days at 5000 bars).
**15m+** wastes the design — the 1m fib becomes a speck and the projection horizon stops being "next move."

### Volatility tuning (HYPE, SOL, low-cap alts)

The default **Min swing size = 1.0 × ATR** can let noisy micro-swings through on choppy crypto. If you see zones flipping more than expected, bump to **1.3–1.5**.

### Newly-listed pairs

The 1D zone needs ~21 confirmed daily bars before it draws (with default `prd=10`). On a pair listed less than ~3 weeks ago on the source exchange, expect the daily box to be empty or anchored to a single early swing.

---

## How to Read It

| What you see                                | What it means                                                   |
|---------------------------------------------|-----------------------------------------------------------------|
| Overlapping zones from multiple TFs         | High-confluence reaction area — strongest mean-reversion targets |
| Price entering a single TF zone in isolation| Lower-confidence reaction — trade with HTF bias                  |
| Projection stub pointing into a zone        | Momentum + HTF trend agree price is heading toward the level     |
| RSI badge red (≥70) at zone tag             | Stretched into resistance — fade candidate                       |
| RSI badge lime (≤30) at zone tag            | Stretched into support — long candidate                          |
| 1m fib line acting as floor/ceiling intrabar| Live entry trigger level                                         |

The script doesn't fire signals — it surfaces structure. Decisions are yours.

---

## Files

- `Stratum.pine` — the indicator source. Paste into TradingView's Pine Editor and add to chart.

---

## Credits

Original Fibonacci OTE Multi-TF framework © **Zeiierman**. Modifications (swing quality filter, projection line, 1m fib level, RSI badge, single-call security refactor, pivot-time correction) layered on top under the same CC BY-NC-SA 4.0 license.

# TradingView Fibonacci Swing Auto-Fib Indicator

A **Pine Script v6** indicator for TradingView that automatically detects the current **bullish or bearish impulse leg** using confirmed market structure and places the **Fibonacci retracement and extension levels** on it — with buy/sell zone signals, a confidence (confluence) score, a historical hit-rate table, and anti-repaint options.

> **GitHub repo description (copy this into the repository "Description" field):**
>
> `TradingView Pine Script v6 indicator that auto-places Fibonacci retracements and extensions on the latest swing impulse, with trend detection, confidence scoring, and historical hit-rate stats.`

**Suggested GitHub topics:** `tradingview` `pinescript` `fibonacci` `technical-analysis` `trading-indicator` `pine-script-v6` `support-resistance`

---

## 🚀 What's new in v2 — this is not a highest-high/lowest-low tool anymore

Version 1 of this indicator simply took the absolute highest high and lowest low of the lookback window. That often anchored the Fibonacci to two unrelated price points.

**Version 2 detects swing-based Fibonacci anchors instead.** It uses confirmed swing highs and swing lows (`ta.pivothigh()` / `ta.pivotlow()`) to find the most recent **real impulse leg**, validates that the leg is meaningful (larger than ATR × a minimum multiplier), checks the trend with an EMA filter, and only then places the Fibonacci on that leg.

## 📌 How it works

### 1. Mode detection — Bullish / Bearish / Neutral

- **Bullish mode:** price is above the EMA trend filter (default EMA 200) **and** there is a valid impulse from a confirmed swing low up to a confirmed swing high.
- **Bearish mode:** price is below the EMA **and** there is a valid impulse from a confirmed swing high down to a confirmed swing low.
- **Neutral:** neither leg qualifies (no impulse big enough, or no confirmed pivots inside the lookback) → no fib is drawn.

An impulse only qualifies if its size is greater than **ATR × "Min Impulse Size"** (default 1.5), which filters out noise legs. Higher-high/higher-low (bullish) or lower-high/lower-low (bearish) swing structure is also checked and feeds the confidence score.

### 2. Bullish fib placement

- Anchored **from the swing low where the up-impulse started to the swing high where it ended** (or is possibly exhausting).
- **0% sits at the swing high**, **100% at the swing low**.
- Retracement levels (23.6%–78.6%) appear inside the leg — pullback zones.
- Extension levels (127.2%–261.8%) project **above the swing high** — bullish continuation targets.
- Anchors are labeled **"Bullish Fib Start"** and **"Bullish Fib End / Possible Sell Zone"**, connected by a diagonal impulse line.

### 3. Bearish fib placement

- Anchored **from the swing high where the down-impulse started to the swing low where it ended**.
- **0% sits at the swing low**, **100% at the swing high**.
- Retracement levels appear inside the leg — bounce/pullback zones.
- Extension levels project **below the swing low** — bearish continuation targets.
- Anchors are labeled **"Bearish Fib Start"** and **"Bearish Fib End / Possible Buy Zone"**.

### Fibonacci levels drawn

`0%` · `23.6%` · `38.2%` · `50%` · `61.8%` · `70.5%` · `78.6%` · `100%` — retracements
`127.2%` · `161.8%` · `200%` · `261.8%` — extensions

## 🎯 Buy / Sell zone signals

- **Potential Sell Zone** (bullish mode): price reaches or rejects the 0% anchor, the prior swing high, or the 127.2%/161.8% extension, **and** at least one confirmation appears — RSI overbought, a bearish rejection candle (long upper wick), or a freshly confirmed pivot high.
- **Potential Buy Zone** (bearish mode): mirror logic at the 0% anchor, prior swing low, or extensions, confirmed by RSI oversold, a bullish rejection candle, or a freshly confirmed pivot low.

Signals fire on **closed bars only** and have a cooldown so the chart doesn't get spammed. Each label shows the current confidence, e.g. **"Potential Sell Zone — Confidence 72%"**.

## 🧮 What the confidence score means

The confidence score (0–100%) is a **confluence score** — it counts how many independent conditions currently agree:

| Component | Max points |
|---|---|
| Trend aligned with the EMA filter | 20 |
| Valid HH/HL (or LH/LL) pivot structure | 15 |
| Impulse strength vs ATR | 15 |
| RSI confirmation (overbought/oversold) | 15 |
| Rejection candle at the zone | 15 |
| Price sitting near a Fibonacci level | 10 |
| Volume above its 20-bar average (if the symbol has volume) | 10 |

**It is not a probability of success and not prediction accuracy.** A 72% score means "many conditions line up right now," not "72% chance the trade works."

## 📊 What the historical hit-rate means

Every signal is tracked and graded after the **Evaluation Bars** window (default 20 bars):

- A **sell** signal is a *hit* if price dropped at least **1 ATR** below the signal close within the window.
- A **buy** signal is a *hit* if price rose at least **1 ATR** above the signal close within the window.

The info table (top-right) shows the current mode, active fib direction, confidence, and the buy/sell hit rates with signal counts. This is a **historical, chart-based statistic for the symbol and timeframe you are viewing**. Past hit rate does **not** guarantee or predict future results — it just tells you how this logic behaved on the visible history.

## ⏳ Why confirmed pivots appear "late" (anti-repaint)

A swing high/low can only be **confirmed** after a number of bars have closed on its right side (the "Pivot Right Bars" input, default 5). Until those bars close, nobody knows whether the swing will hold. That confirmation delay is the price of reliability — it prevents the indicator from painting anchors that later vanish.

Two anchor modes are available:

- **Confirmed** (default) — anchors move only to confirmed pivots. Safest, least repainting, but delayed by the right-bar count.
- **Early** — if price pushes beyond the last confirmed anchor, the fib end follows the developing extreme immediately. Faster, but that anchor can keep moving (repaint) until a new pivot confirms.

No future-leaking functions are used anywhere, and signals/alerts only fire on closed bars.

## ⚙️ Inputs

| Input | Default | Purpose |
|---|---|---|
| Pivot Left / Right Bars | 5 / 5 | Swing detection sensitivity; right bars = confirmation delay |
| Lookback Bars | 200 | Pivots older than this are ignored |
| Min Impulse Size (× ATR) | 1.5 | A leg must beat ATR × this to count as an impulse |
| Anchor Mode | Confirmed | Confirmed (safe/delayed) or Early (fast/may repaint) |
| EMA Trend Filter Length | 200 | Above = bullish bias, below = bearish bias |
| RSI Length | 14 | Overbought/oversold confirmation |
| Evaluation Bars | 20 | Hit-rate grading window |
| Show Bullish / Bearish Fibs | on | Toggle each direction |
| Show Labels / Info Table | on | Toggle text and stats table |
| Extend Fib Lines Right | off | Extend levels to the right edge |
| Bullish / Bearish Fib Color | teal / red | Direction colors |

## 🛠️ How to install it in TradingView

1. Open [TradingView](https://www.tradingview.com/) and open any chart.
2. Click **Pine Editor** at the bottom of the chart.
3. Delete any placeholder code in the editor.
4. Open [`fibonacci_high_low_indicator.pine`](fibonacci_high_low_indicator.pine) from this repository and copy **all** of its contents.
5. Paste it into the Pine Editor, click **Save**, then **Add to chart**.
6. Adjust the settings via the ⚙ gear icon on the indicator.

## 🔔 Alerts

Right-click the chart → **Add alert** → Condition: *Fibonacci Swing Auto-Fib* → choose:

- New Bullish Fib Detected
- New Bearish Fib Detected
- Potential Sell Zone
- Potential Buy Zone
- Price Touched Fib 38.2% / 50% / 61.8% / 78.6%

## 📷 Screenshots

> Add your own screenshots here after adding the indicator to a chart.

| Description | Image |
|---|---|
| Bullish fib on an uptrend leg | `![Bullish fib](screenshots/bullish-fib.png)` |
| Bearish fib with buy-zone signal | `![Bearish fib](screenshots/bearish-fib.png)` |
| Info table with hit-rate stats | `![Stats table](screenshots/stats-table.png)` |

## ⚠️ Disclaimer

This indicator is provided **for educational and informational purposes only**. It is **not financial advice** and it **does not guarantee accuracy or profits**. The confidence score is a confluence measure, the hit rate is a historical statistic, and neither predicts future price movement. Fibonacci levels show areas where price *may* react — not where it *will*. Trading involves substantial risk of loss; always do your own research and manage your risk.

## 📄 License

Released under the [MIT License](LICENSE).

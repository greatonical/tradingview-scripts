# TradingView README.md
## Moving Average Cross Buy/Sell Strategies (Pine Script v6)

This repository contains **two TradingView Pine Script strategy files** that implement simple moving‑average (SMA) cross systems with on‑chart labels, visual markers, and ready‑to‑use alert conditions:

- **`Simple Auto Buy-Sell Strategy with Price.pine`** — minimal SMA cross strategy with entry markers and alerts.
- **`Advanced Buy-Sell Strategy.pine`** — expanded version that adds explicit **entry/exit labels with prices** for both long and short, plus extra visual shapes and alert conditions to mirror each action.

Both scripts use **Pine Script v6** and run as **strategies** (so you can backtest in TradingView).

---

## 1) What the strategies do

### Core idea
- Calculate two SMAs: a **short** (fast) SMA and a **long** (slow) SMA.
- **Go Long** when the short SMA **crosses above** the long SMA (`ta.crossover`).
- **Go Short / Close Long** when the short SMA **crosses below** the long SMA (`ta.crossunder`).

### Shared features
- Plots the short SMA (blue) and long SMA (red) on the price chart.
- Uses **`strategy()`** so you can **backtest** and see PnL, win rate, drawdown, etc.
- Adds **plotshape** markers to show buy/sell signals on the chart.
- Declares **alert conditions** so you can build TradingView alerts (e.g., webhook to a bot).

### What’s different in each file

| File | Entry/Exit Logic | Labels With Price | Visual Shapes | Alerts |
|---|---|---|---|---|
| `Simple Auto Buy-Sell Strategy with Price.pine` | Long on `crossover`, (implicitly) short/exit on `crossunder` | Entry price label on signal | Buy (green label-up), Sell (red label-down) | “Go Long” and “Go Short” |
| `Advanced Buy-Sell Strategy.pine` | Long on `crossover`, Short on `crossunder`, **explicit close signals** for both sides | **Entry and Close** labels for **both Long and Short**, each showing `@ price` | Multiple markers: buy/sell/short/close short | Four alerts: **Go Long**, **Close Long**, **Go Short**, **Close Short** |

> Note: In backtests, entries and exits follow the **bar close** where the crossover/crossunder is confirmed, unless TradingView’s “Recalculate on every tick” is enabled.

---

## 2) File walkthroughs

### `Simple Auto Buy-Sell Strategy with Price.pine`
- **Indicators**: `shortMA = ta.sma(close, 10)`, `longMA = ta.sma(close, 30)`.
- **Long entry**: `longCondition = ta.crossover(shortMA, longMA)` → `strategy.entry("Long", strategy.long)`.
- **Sell/short signal**: `shortCondition = ta.crossunder(shortMA, longMA)` (used for markers/alerts).
- **On‑chart visuals**:
  - `plot(shortMA, color=color.blue)` and `plot(longMA, color=color.red)`.
  - `plotshape(longCondition, style=shape.labelup, color=color.green, title="Buy Signal")`.
  - `plotshape(shortCondition, style=shape.labeldown, color=color.red, title="Sell Signal")`.
  - Adds a **price label** when a long signal fires (e.g., “BUY @ 1234.56”).
- **Alerts**:
  - `alertcondition(longCondition,  title="Go Long",  message="Go Long @ {{close}}")`
  - `alertcondition(shortCondition, title="Go Short", message="Go Short @ {{close}}")`

### `Advanced Buy-Sell Strategy.pine`
- **Indicators**: same two SMAs (`10` and `30`) and plots.
- **Signals**:
  - `longCondition  = ta.crossover(shortMA, longMA)` → **enter Long**, **close Short**.
  - `shortCondition = ta.crossunder(shortMA, longMA)` → **enter Short**, **close Long**.
- **On‑chart visuals & labels**:
  - **Entry labels with price** for Long/Short (e.g., “LONG @ 1234.56”, “SHORT @ 1234.56”).  
  - **Close labels with price** for Long/Short (e.g., “CLOSE L @ …”, “CLOSE S @ …”).  
  - Extra `plotshape` markers for buy/sell/short/close confirmations.
- **Alerts**:
  - `alertcondition(longCondition,  title="Go Long",     message="Go Long @ {{close}}")`
  - `alertcondition(shortCondition, title="Close Long",  message="Close Long @ {{close}}")`
  - `alertcondition(shortCondition, title="Go Short",    message="Go Short @ {{close}}")`
  - `alertcondition(longCondition,  title="Close Short", message="Close Short @ {{close}}")`

---

## 3) How to use in TradingView

1. **Open Pine Editor** (lower panel) in TradingView.
2. Click **Open** → **Upload** (or copy‑paste the code) for the script you want to run.
3. Press **Save** (pick any name) then **Add to chart**.
4. In the **Strategy Tester** tab, you’ll see backtest results (Net Profit, Max Drawdown, List of Trades, etc.).
5. To change MA lengths, edit the numbers in `ta.sma(close, 10)` and `ta.sma(close, 30)` (or convert them to `input.int` if you want a settings UI).

> Tip: On lower timeframes (e.g., 1‑minute), MA crosses can produce frequent signals. Consider higher timeframes or longer MA lengths if you want fewer trades.

---

## 4) Alerts setup

Both scripts only **define** alert conditions; TradingView requires you to create an alert instance:

1. With the strategy added to chart, click **Alerts** (clock icon) → **Create Alert**.
2. In **Condition**, select the strategy name and the **specific alert** (e.g., “Go Long”, “Go Short”, etc.).
3. Choose **Once per bar** or **Once per bar close** (recommended for MA crosses).
4. (Optional) Set **Webhook URL** to notify your bot/server.
5. The **message** will include the `{{close}}` placeholder resolved by TradingView to the bar’s close price.

---

## 5) Assumptions & limitations

- **No stop loss / take profit** is coded. Add `strategy.exit()` or manage risk externally.
- **No position sizing inputs**. TradingView’s default order size applies (change in Strategy Properties).
- **No filters** (RSI, volume, trend) — purely MA cross logic.
- Cross signals are evaluated **on bar data**. If you enable “recalculate on every tick,” signals may differ intra‑bar.
- If you need **only entries** (no shorting) or **only exits**, you can comment out specific `strategy.entry`/`strategy.close` lines in the advanced file.

---

## 6) Customization ideas (quick edits)

- **Make SMA lengths configurable**:
  ```pine
  fastLen = input.int(10, "Fast SMA")
  slowLen = input.int(30, "Slow SMA")
  shortMA = ta.sma(close, fastLen)
  longMA  = ta.sma(close, slowLen)
  ```
- **Add stop loss / take profit** (example, 1% SL & 2% TP):
  ```pine
  strategy.exit("L-Exit", from_entry="Long", stop=strategy.position_avg_price * 0.99, limit=strategy.position_avg_price * 1.02)
  strategy.exit("S-Exit", from_entry="Short", stop=strategy.position_avg_price * 1.01, limit=strategy.position_avg_price * 0.98)
  ```
- **Reduce signal noise**: lengthen SMAs (e.g., 20/50), add a higher‑timeframe trend filter, or require price above/below long MA to take trades.

---

## 7) Troubleshooting

- **“No trades appear”**: Ensure you added the script as a **Strategy** (not an Indicator), and that the symbol/timeframe has enough bars.
- **“Too many/too few trades”**: Adjust MA lengths or timeframe; enable “Once per bar close” on alerts to avoid intra‑bar whipsaws.
- **“Orders don’t match my broker fills”**: TradingView backtests simulate fills at bar close (unless otherwise configured) — they won’t perfectly match a live broker.
- **“Labels clutter the chart”**: Comment out `label.new` or `plotshape` lines you don’t need.

---

## 8) File list

- `/mnt/data/Simple Auto Buy-Sell Strategy with Price.pine`
- `/mnt/data/Advanced Buy-Sell Strategy.pine`

> You can rename/move them as you like after downloading.

---

## 9) License & attribution

These example scripts are provided **as‑is** for educational purposes. Use at your own risk. No warranty. Not financial advice.


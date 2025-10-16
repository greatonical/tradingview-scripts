# Confluence Trendwave

Links: 
Moving Confluence - [https://github.com/greatonical/tradingview-scripts/blob/main/scripts/Moving Confluence.pine](https://github.com/greatonical/tradingview-scripts/blob/main/scripts/Moving%20Confluence.pine)
Normal Confluence - [https://github.com/greatonical/tradingview-scripts/blob/main/scripts/Normal Confluence.pine](https://github.com/greatonical/tradingview-scripts/blob/main/scripts/Normal%20Confluence.pine)
Momentum Suite (Open in a new window in TradingView and used with either confluence) - [https://github.com/greatonical/tradingview-scripts/blob/main/scripts/Momentum-sUITE.pine](https://github.com/greatonical/tradingview-scripts/blob/main/scripts/Momentum-Suite.pine)
---

## **⚡ 1. Moving Confluence (Dynamic)**

> Purpose:
> 

**How it works:**

- Combines **EMA Ribbon (trend)** + **MACD (momentum)** + **StochRSI (timing)**.
- When all three align → shows **LONG** or **SHORT** instantly.
- Signals adjust or disappear as the market changes — it’s **real-time and flexible**.
- Great for **scalping, short-term trades**, or live monitoring.

**Key traits:**

- Always up-to-date with current conditions.
- Entry, stop, and targets **move** with market data.
- Can repaint slightly because it’s adaptive.

**Think of it as:**

🧭 “The live weather radar” — constantly updating to show what’s happening **now**.

---

## **🧱 2. Normal Confluence (Latched Plan)**

> Purpose:
> 

**How it works:**

- Same confluence logic (EMA + MACD + StochRSI),
    
    but when a strong setup appears →
    
    it **locks the trade plan** (entry, stop, targets).
    
- It stays fixed even if price moves, until:
    - an **opposite signal** triggers, or
    - the **trend flips**, or
    - you manually reset it.

**Key traits:**

- Entry/TP/Stop lines **don’t move** once set.
- Great for **swing trades** or structured setups.
- Helps with discipline and backtesting — no constant repainting.

**Think of it as:**

🎯 “The battle plan” — once the signal forms, it’s fixed until the next big shift.

---

### **🔁 Quick Comparison**

| **Feature** | **Moving Confluence** | **Normal Confluence** |
| --- | --- | --- |
| Signal updates | Every candle | Only on new confirmed setup |
| Entry/TP lines | Move dynamically | Locked/fixed |
| Ideal for | Live scalping, intraday | Swing & planned trades |
| Risk | Can repaint | Stable but slower to adapt |
| Behavior | Reactive | Strategic |

---

**In short:**

- Use **Moving Confluence** to *read* the market’s current direction.
- Use **Normal Confluence** to *act* on a complete, confirmed setup and stick with it.

# Technical Explanation

---

## **⚡ 1.**

## **Moving Confluence (Dynamic)**

> Real-time reaction — all signals refresh every bar.
> 

### **🧩 Components & Alignment Logic**

### **(1) EMA Ribbon — Trend Direction**

- Uses **fast EMA (9)** and **slow EMA (21)**.
- When **fast EMA > slow EMA → bullish trend** ✅
- When **fast EMA < slow EMA → bearish trend** 🔻
    
    → This defines whether we’re looking for **Longs** or **Shorts**.
    

### **(2) MACD — Momentum Confirmation**

- Measures how strong the trend is.
- **MACD line = EMA(12) − EMA(26)**
- **Signal line = EMA(MACD line, 9)**
- **Histogram = MACD line − Signal line**

**Alignment:**

- **Bullish:** MACD line above signal & histogram > 0
- **Bearish:** MACD line below signal & histogram < 0

→ Confirms that **momentum supports the trend** seen in the EMA ribbon.

If EMA says “uptrend” but MACD is below zero — the move is weak or losing power.

### **(3) StochRSI — Timing**

- RSI measures speed/strength of price; StochRSI re-normalizes it.
- **%K** and **%D** are smoothed RSI values (14,3 by default).

**Alignment:**

- **Bullish timing:**
    - %K crosses **above** %D
    - OR both are in **oversold zone (<20)** → potential upward reversal
- **Bearish timing:**
    - %K crosses **below** %D
    - OR both are in **overbought zone (>80)** → potential downward reversal

→ This tells you *when* to enter within the trend.

### **⚙️**

### **Combined Logic (per bar):**

| **Signal** | **EMA** | **MACD** | **StochRSI** | **Meaning** |
| --- | --- | --- | --- | --- |
| **LONG** | Fast > Slow | MACD > Signal & Hist > 0 | %K > %D or <20 | Trend + Momentum + Timing agree |
| **SHORT** | Fast < Slow | MACD < Signal & Hist < 0 | %K < %D or >80 | Trend + Momentum + Timing agree |

So, a **LONG** appears only when:

→ the **trend** is bullish, **momentum** supports it, and **timing** shows upward pressure.

A **SHORT** appears when the opposite three align.

---

### **🧠 Behavior Summary**

- Updates every candle — can flip if one piece changes.
- Entry/targets move with price because conditions change bar-to-bar.
- Useful for **spotting trend continuations** and short-term reversals quickly.

**In practice:**

> “When EMA says we’re trending up, MACD confirms strength, and StochRSI turns from oversold — go LONG.”
> 

---

## **🧱 2.**

## **Normal Confluence (Latched Plan)**

> Locks a setup when all conditions align — stays fixed until reset.
> 

The **same three indicators** are used, but logic is applied only once when a **strong full alignment** appears.

---

### **🧩 Components & Alignment Logic (same foundation)**

### **EMA Ribbon (Trend Filter)**

- Confirms the *side* of the market to trade.
- Only when **fast EMA crosses and stays above slow EMA** → bullish mode.
- Only when **fast EMA below slow EMA** → bearish mode.

### **MACD (Momentum Strength)**

- Must confirm the trend bias before a signal “arms.”
- For a **bullish setup:** MACD > Signal and Histogram > 0
- For a **bearish setup:** MACD < Signal and Histogram < 0

This ensures we don’t enter against momentum.

### **StochRSI (Entry Trigger)**

- Acts as the *final trigger* before latching.
- For a **LONG plan:**
    - %K crosses above %D **from oversold region (<20)**.
- For a **SHORT plan:**
    - %K crosses below %D **from overbought region (>80)**.

This prevents early entries — you wait until price starts to turn with the trend.

---

### **⚙️**

### **Combined “Latch” Logic**

| **Setup** | **EMA** | **MACD** | **StochRSI** | **Action** |
| --- | --- | --- | --- | --- |
| **Long Plan** | Fast > Slow | MACD > Signal & Hist > 0 | %K cross up from <20 | Locks a LONG plan |
| **Short Plan** | Fast < Slow | MACD < Signal & Hist < 0 | %K cross down from >80 | Locks a SHORT plan |

Once that setup appears:

- **Entry = current price**
- **Stop = ± ATR×Multiplier**
- **TP1–TP5 = fixed % levels**
- Plan stays until an **opposite setup**, **trend flip**, or **manual reset**.

---

### **🧠 Behavior Summary**

- One clean, confirmed setup — no repaint.
- Targets/Stops don’t move with price.
- Ideal for **swing trades** or **backtesting** stable systems.

**In practice:**

> “When EMA trend, MACD momentum, and StochRSI timing all line up — lock your trade plan and stick with it until the market truly changes.”
> 

---

## **⚖️ Side-by-Side Quick Comparison**

| **Aspect** | **Moving Confluence** | **Normal Confluence** |
| --- | --- | --- |
| EMA role | Live trend filter | Entry-side lock |
| MACD role | Real-time momentum confirmation | Trend-momentum confirmation before locking |
| StochRSI role | Timing every bar | One-time trigger at setup |
| Signal type | Reactive per bar | Fixed plan |
| Updates | Constant | Only when reset |
| Use case | Short-term, live reading | Swing, structured trades |

---

✅ **In short:**

Both use the same **3-point alignment system** —

**EMA for direction, MACD for strength, StochRSI for timing** —

but the **Moving Confluence** keeps evolving, while the **Normal Confluence** locks and commits.
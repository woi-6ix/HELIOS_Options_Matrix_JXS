# HELIOS Options Matrix Dashboard

HELIOS Matrix is a Streamlit-based options trading research dashboard for paper trading. It combines price-data analysis, regime classification, option-chain scanning, spread generation, FinBERT news sentiment, and an XGBoost paper-trade ranker.

The app is designed for defined-risk options strategies, especially:

- 0DTE iron condors
- Iron condors
- Credit spreads
- Debit spreads
- Butterfly-style neutral setups
- Paper-trade journaling and ML ranking

> Educational / paper-trading use only. This app does not connect to a broker and does not place trades.

---

## Features

### 1. Regime Dashboard

The regime classifier analyzes each ticker using:

- Price trend
- Moving averages
- Realized volatility
- ATR
- ATM implied volatility estimate
- IV rank proxy
- Event-risk window

It classifies tickers into regimes such as:

- Sideways / High IV
- Sideways / Low IV
- Bullish / High IV
- Bullish / Low IV
- Bearish / High IV
- Bearish / Low IV
- Volatile / Directional
- Event Risk
- Mixed / Wait

The goal is to connect the market environment to the most appropriate paper-trading structure.

Example:

| Ticker | Regime | Suggested Setup |
|---|---|---|
| SPY | Sideways / High IV | Iron condor or credit spread candidate |
| QQQ | Bullish / Low IV | Call debit spread candidate |
| AAPL | Event Risk | Avoid or reduce paper size |

---

## 2. Option Chain + Greeks

The dashboard pulls option-chain data using `yfinance`.

For each option contract, the app calculates:

- Strike
- Bid
- Ask
- Mid price
- Bid-ask spread %
- Volume
- Open interest
- Implied volatility
- Approximate delta
- Approximate theta
- Approximate vega
- Liquidity score

Greeks are estimated using a Black-Scholes approximation.

---

## 3. Spread Scanner

The spread scanner generates and ranks possible spreads.

Supported spread types:

- Bull Put Credit Spread
- Bear Call Credit Spread
- Bull Call Debit Spread
- Bear Put Debit Spread
- Iron Condor Candidate

The scanner ranks candidates using:

- Regime fit
- Liquidity score
- Reward-to-risk ratio
- DTE
- Short delta
- Net delta
- Net theta
- Net vega
- IV rank proxy
- ATR
- Realized volatility

The scanner table is sorted by highest final score first. The highest final score is highlighted in green.

---

## 4. 0DTE Iron Condor Support

HELIOS supports 0DTE iron condor scanning.

The sidebar includes:

```python
zero_dte_only = st.checkbox(
    "0DTE iron condor focus",
    value=True,
    help="When enabled, HELIOS prioritizes same-day expirations for iron condor scanning. If no 0DTE chain is available, it falls back to the DTE range below.",
)

# HELIOS Matrix Options Dashboard ☀️📊

![Python](https://img.shields.io/badge/Python-3.11%2B-blue)
![License](https://img.shields.io/badge/License-MIT-purple)
![Framework](https://img.shields.io/badge/Framework-Streamlit-red)
![Model](https://img.shields.io/badge/Model-FinBERT%20%2B%20XGBoost-black)
![Data](https://img.shields.io/badge/Data-Yahoo%20Finance-green)
![Options](https://img.shields.io/badge/Options-0DTE%20Iron%20Condors-orange)
![Status](https://img.shields.io/badge/Mode-Paper%20Trading%20Only-yellow)

**HELIOS Matrix Options Dashboard**, named after Helios, the Greek personification of the Sun, is a Streamlit-based options research dashboard that combines Yahoo Finance market data, option-chain scanning, regime classification, approximate Greeks, FinBERT news sentiment, 0DTE iron condor support, and an XGBoost paper-trade ranker.

URL: [https://helios-matrix-jxs.streamlit.app/](https://helios-matrix-jxs.streamlit.app/)

---

## 📌 Project Overview

**HELIOS Matrix Options Dashboard** is an interactive options analytics web application built with Python and Streamlit. The app allows users to scan liquid tickers, classify the current market regime, pull option chains, calculate approximate Greeks, generate spread candidates, evaluate 0DTE iron condor setups, analyze daily news sentiment, and journal paper trades.

This project was created as an educational machine learning and options research dashboard. It combines quantitative market analysis, volatility regime classification, options spread construction, natural language processing, and paper-trade journaling in one clean web interface.

> This application is for **paper trading, education, and research only**. It does not connect to a broker and does not place live trades.

---

## 🔍 What the App Does

The dashboard walks through a full options scanning and paper-trading workflow:

1. **User enters one or more tickers**

   * Example tickers: `SPY`, `QQQ`, `IWM`, `AAPL`, `MSFT`, `NVDA`
   * XSP note: `^XSP` may not return option chains reliably through `yfinance`, even though XSP options exist.

2. **Yahoo Finance market data is fetched**

   * The app retrieves historical daily price data using `yfinance`.
   * Price data is cached with Streamlit caching.
   * Yahoo Finance output is normalized so price columns work reliably.

3. **Technical indicators are calculated**

   * Daily returns
   * Simple moving averages
   * ATR
   * Realized volatility
   * Trend strength
   * Trend direction

4. **A regime classifier labels the market environment**

   * The app classifies each ticker into regimes such as:

     * `Sideways / High IV`
     * `Sideways / Low IV`
     * `Bullish / High IV`
     * `Bullish / Low IV`
     * `Bearish / High IV`
     * `Bearish / Low IV`
     * `Volatile / Directional`
     * `Event Risk`
     * `Mixed / Wait`

5. **Option chains are pulled**

   * The app retrieves available option expirations and chains through `yfinance`.
   * It cleans call and put chains.
   * It calculates bid/ask metrics, mid prices, DTE, moneyness, and liquidity scores.

6. **Approximate Greeks are calculated**

   * The app estimates:

     * Delta
     * Theta
     * Vega

   * Greeks are calculated using a Black-Scholes approximation.

7. **Spread candidates are generated**

   * The app generates:

     * Bull put credit spreads
     * Bear call credit spreads
     * Bull call debit spreads
     * Bear put debit spreads
     * Iron condor candidates

8. **0DTE iron condors are supported**

   * Same-day expirations can be prioritized.
   * 0DTE iron condors receive separate scoring logic.
   * Balanced net delta is rewarded.
   * The app warns about gamma and pin risk.

9. **Spreads are ranked**

   * Candidates are scored using:

     * Regime fit
     * Liquidity
     * Reward-to-risk
     * DTE
     * Short delta
     * Net delta
     * Net theta
     * Net vega
     * IV rank proxy
     * ATR
     * Realized volatility

10. **Paper trades are journaled**

    * Scanner candidates can be added to a paper-trade journal.
    * Manual paper trades can also be entered.
    * Exit premium, realized P/L, mistake notes, and labels can be saved.

11. **XGBoost ranker learns from the journal**

    * Once enough labeled paper trades exist, the app trains an XGBoost classifier.
    * Future spread candidates can be ranked using both rules and the model.

12. **FinBERT news sentiment is analyzed**

    * Yahoo Finance RSS articles are scored with FinBERT.
    * The app displays article dropdowns, sentiment scores, risk flags, a sentiment donut chart, and a news-to-spread readout.

---

## ✨ Key Features

* 📊 **Interactive Streamlit Interface**  
  Clean dashboard layout with sidebar controls, tabs, scanner results, data tables, charts, and journal tools.

* ☀️ **HELIOS Branding**  
  Named after Helios, reflecting the app's purpose of bringing clarity and visibility to options market conditions.

* ⚡ **0DTE Iron Condor Focus**  
  Same-day expirations can be prioritized for 0DTE iron condor scanning when option-chain data is available.

* 🧭 **Market Regime Classifier**  
  Uses trend, realized volatility, ATR, IV proxy, and event risk to identify whether the market looks bullish, bearish, sideways, volatile, or risky.

* 📈 **Yahoo Finance Market Data**  
  Retrieves historical price data and option chains using the `yfinance` library.

* 🧮 **Approximate Options Greeks**  
  Calculates delta, theta, and vega using a Black-Scholes approximation.

* 🧱 **Spread Generator**  
  Builds vertical spreads and combines compatible put/call credit spreads into iron condor candidates.

* 🟢 **Ranked Scanner Output**  
  Sorts spread candidates by highest final score first and highlights the top score in green.

* 🧠 **XGBoost Paper-Trade Ranker**  
  Learns from labeled paper-trade journal outcomes once enough examples exist.

* 📰 **FinBERT News Sentiment Add-On**  
  Uses Hugging Face's `ProsusAI/finbert` model to classify Yahoo Finance RSS articles as positive, neutral, or negative.

* ⚠️ **News Risk Flags**  
  Flags articles mentioning earnings, guidance, macro events, Fed/rates, lawsuits, analyst ratings, M&A, or volatility shocks.

* 📝 **Paper Trade Journal**  
  Tracks model score, rule score, Greeks, entry/exit premium, realized P/L, exit reason, labels, and mistake notes.

* 🛡️ **Error Handling**  
  Includes safeguards for missing option chains, invalid tickers, empty data, unavailable sentiment libraries, insufficient model data, and Streamlit Cloud issues.

---

## 🧠 Model Methodology

HELIOS combines three major modelling components:

1. Regime classification
2. Options spread scoring
3. Machine learning-based spread ranking

---

### 1. Regime Classification

The app classifies each ticker based on technical and volatility features.

| Feature | Description |
| ------- | ----------- |
| SMA 20 | Short-term moving average |
| SMA 50 | Medium-term moving average |
| SMA 200 | Long-term trend reference |
| ATR % | Average true range as a percentage of price |
| RV20 | 20-day realized volatility |
| RV60 | 60-day realized volatility |
| Trend Strength | Price distance from SMA 50 relative to ATR |
| ATM IV | Estimated at-the-money implied volatility from option chain |
| IV Rank Proxy | Current ATM IV compared against recent realized volatility range |
| Event Risk | Earnings or event date near the current date |

The regime classifier then maps these features into strategy context.

Example:

| Regime | Possible Setup |
| ------ | -------------- |
| Sideways / High IV | Iron condor or credit spread candidate |
| Sideways / Low IV | Butterfly or small debit spread candidate |
| Bullish / High IV | Bull put credit spread candidate |
| Bullish / Low IV | Call debit spread candidate |
| Bearish / High IV | Bear call credit spread candidate |
| Bearish / Low IV | Put debit spread candidate |
| Event Risk | Avoid or reduce size |

---

### 2. IV Rank Proxy

HELIOS does **not** calculate true historical IV rank.

Instead, it estimates an IV rank proxy:

```text
Current ATM IV compared against the recent realized-volatility range
```

This is useful for rough screening but should not be treated as broker-grade IV rank.

---

### 3. Option Chain Cleaning

For each option chain, the app calculates:

| Column | Description |
| ------ | ----------- |
| Strike | Option strike price |
| Bid | Current bid |
| Ask | Current ask |
| Mid | Midpoint between bid and ask |
| Bid-Ask Spread | Ask minus bid |
| Bid-Ask Spread % | Spread as a percentage of mid price |
| Volume | Current volume |
| Open Interest | Open contracts |
| Implied Volatility | Yahoo Finance implied volatility |
| DTE | Days to expiration |
| Moneyness % | Strike distance from underlying price |
| Liquidity Score | Composite liquidity metric |

---

### 4. Greek Calculation

HELIOS uses a Black-Scholes approximation for option Greeks.

The app estimates:

| Greek | Description |
| ----- | ----------- |
| Delta | Approximate directional exposure |
| Theta | Approximate daily time decay |
| Vega | Approximate volatility sensitivity |

These are approximate and may differ from broker platform Greeks.

---

### 5. Spread Generation

HELIOS generates possible spreads by pairing option legs.

Supported strategies:

| Strategy | Structure |
| -------- | --------- |
| Bull Put Credit Spread | Sell higher-strike put, buy lower-strike put |
| Bear Call Credit Spread | Sell lower-strike call, buy higher-strike call |
| Bull Call Debit Spread | Buy lower-strike call, sell higher-strike call |
| Bear Put Debit Spread | Buy higher-strike put, sell lower-strike put |
| Iron Condor Candidate | Combine bull put credit spread + bear call credit spread |

---

### 6. Rule-Based Spread Scoring

Before enough journal data exists, HELIOS uses rule-based scoring.

The score considers:

* Regime fit
* Liquidity score
* Reward-to-risk
* Short delta
* Net delta
* Net theta
* Net vega
* DTE
* 0DTE iron condor focus
* Gamma-risk penalties
* Balanced 0DTE delta

The scanner output is sorted by `final_score` from highest to lowest.

---

### 7. XGBoost Spread Ranking

Once the journal has enough labeled paper trades, HELIOS trains an XGBoost classifier.

The model uses:

* DTE
* Width
* Premium
* Max profit
* Max loss
* Reward-to-risk
* Net delta
* Net theta
* Net vega
* Short delta
* Liquidity score
* Bid-ask spread %
* IV rank proxy
* ATM IV
* RV20
* RV60
* ATR %
* Trend strength
* Regime code
* Strategy code

The model target is whether a paper trade was successful.

Successful labels:

* `hit_50_profit`
* `expired_profitable`
* `closed_profitable`

Failure labels:

* `hit_max_loss`
* `closed_loss`

---

## 📊 Technical Indicators

HELIOS calculates technical indicators from historical daily price data.

| Indicator | Description |
| --------- | ----------- |
| Daily Return | Percent change from previous close |
| SMA 5 | Very short-term price average |
| SMA 10 | Short-term price average |
| SMA 20 | Short-term trend filter |
| SMA 50 | Medium-term trend filter |
| SMA 100 | Longer-term trend filter |
| SMA 200 | Long-term trend filter |
| ATR 14 | 14-day average true range |
| ATR % | ATR divided by price |
| RV 20 | 20-day annualized realized volatility |
| RV 60 | 60-day annualized realized volatility |
| Trend 20d % | 20-day price change |
| Trend 50d % | 50-day price change |
| Trend Strength | Price distance from SMA 50 divided by ATR |

---

## 📰 News Sentiment Analysis

The FinBERT sentiment tab analyzes Yahoo Finance RSS articles.

For each matched article, the dashboard shows:

* Published date
* Article title
* Sentiment label
* Sentiment score
* News bias score
* Risk flags
* Article link
* Article summary

The app also displays:

| Metric | Description |
| ------ | ----------- |
| Articles | Number of matched articles |
| Average Sentiment | Mean FinBERT score |
| News Bias Score | Directional score where neutral sentiment contributes 0 |
| Positive / Neutral / Negative | Article count by sentiment direction |

---

## ⚠️ News-to-Spread Readout

The dashboard converts the daily news readout into possible spread context.

| News / Volatility Context | Possible Spread Bias |
| ------------------------- | -------------------- |
| Bullish news + high IV | Bull put credit spread candidate |
| Bullish news + low IV | Call debit spread candidate |
| Bearish news + high IV | Bear call credit spread candidate |
| Bearish news + low IV | Put debit spread candidate |
| Neutral news + high IV | Iron condor candidate |
| Neutral news + low IV | Butterfly candidate |
| Event-risk warning | Avoid or reduce size |

Risk flags include:

* Earnings / Guidance
* Macro / Fed / Rates
* Analyst / Rating Risk
* Legal / Regulatory
* M&A / Corporate Action
* Volatility / Shock

---

## 📁 Project Structure

```bash
heliosmatrix_jxs/
│
├── HeliosMatrix_JXS.py        # Main Streamlit application
├── requirements.txt           # Python package dependencies
├── README.md                  # Project documentation
│
└── .streamlit/
    └── config.toml            # Optional Streamlit configuration
```

---

## 🛠️ Tech Stack

| Category | Tools |
| -------- | ----- |
| Programming Language | Python |
| Web Framework | Streamlit |
| Price Data | Yahoo Finance via `yfinance` |
| Option Chains | Yahoo Finance via `yfinance` |
| News Source | Yahoo Finance RSS via `feedparser` |
| NLP Model | FinBERT through Hugging Face Transformers |
| Spread Ranker | XGBoost Classifier |
| Data Handling | pandas, numpy |
| Visualization | matplotlib, Streamlit tables |
| Model Evaluation | scikit-learn |
| Deployment | Streamlit Community Cloud |

---

## 📦 Installation

### 1. Clone the Repository

```bash
git clone https://github.com/YOUR_USERNAME/heliosmatrix_jxs.git
cd heliosmatrix_jxs
```

If your GitHub repository name is different, replace the URL and folder name with your actual repo.

---

### 2. Create a Virtual Environment

```bash
python -m venv venv
```

Activate the virtual environment:

**Windows**

```bash
venv\Scripts\activate
```

**Mac/Linux**

```bash
source venv/bin/activate
```

---

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

---

### 4. Run the Application

```bash
streamlit run HeliosMatrix_JXS.py
```

---

## 📄 requirements.txt

Use a simple requirements file instead of a full `pip freeze` export:

```txt
streamlit
pandas
numpy
matplotlib
yfinance
scikit-learn
xgboost
feedparser
transformers
torch
requests
```

### Python Version Note

For Streamlit Cloud, dependency compatibility can depend on the Python version selected during deployment.

If Streamlit Cloud uses a very new Python version, avoid pinning old exact versions of `torch`, `xgboost`, or `scikit-learn`.

---

## ⚙️ Optional Streamlit Config

Create a `.streamlit/config.toml` file:

```toml
[server]
fileWatcherType = "none"
```

This helps reduce Streamlit file-watcher issues with PyTorch internals on hosted deployments.

The app also sets:

```python
os.environ.setdefault("STREAMLIT_SERVER_FILE_WATCHER_TYPE", "none")
os.environ.setdefault("TOKENIZERS_PARALLELISM", "false")
```

---

## 🚀 Deployment on Streamlit Cloud

To deploy the app:

1. Push all project files to GitHub.
2. Go to Streamlit Community Cloud.
3. Create a new app.
4. Select the GitHub repository.
5. Set the main file path to:

```bash
HeliosMatrix_JXS.py
```

6. Make sure `requirements.txt` is in the repo root.
7. Deploy the app.
8. If dependencies fail, update `requirements.txt`, commit, push, and reboot the app.

Recommended repository files for deployment:

```bash
HeliosMatrix_JXS.py
requirements.txt
.streamlit/config.toml
README.md
```

---

## 🖥️ How to Use the App

1. Open the Streamlit app.
2. Enter one or more tickers.

   * Example: `SPY, QQQ, IWM, AAPL`

3. Select the price history period.
4. Enable or disable **0DTE iron condor focus**.
5. Set your DTE range.
6. Set your spread width range.
7. Choose liquidity filters:

   * Max bid-ask spread %
   * Minimum open interest
   * Minimum credit/debit

8. Choose whether to build iron condor candidates.
9. Click **Run HELIOS Matrix Scan**.
10. Review the tabs:

   * Regime Dashboard
   * Option Chain + Greeks
   * Spread Scanner
   * Paper Trade Journal + ML Ranker
   * FinBERT Sentiment

---

## 🎛️ Sidebar Controls

The sidebar is the main control panel for the scanner.

| Control | Description |
| ------- | ----------- |
| Tickers | Comma-separated list of tickers to scan |
| Price history period | Historical data range used for trend and volatility calculations |
| 0DTE iron condor focus | Prioritizes same-day expirations when available |
| DTE range | Filters option expirations by days to expiration |
| Spread width range | Controls minimum and maximum strike width |
| Max bid-ask spread % per leg | Filters out illiquid options |
| Min open interest per leg | Filters out low open-interest contracts |
| Min credit/debit per share | Sets minimum spread premium |
| Build iron condor candidates | Combines compatible call and put credit spreads |
| Event-risk window days | Flags tickers with upcoming events |
| Max expirations per ticker | Limits scan size for faster performance |
| Run HELIOS Matrix Scan | Starts the full scanner workflow |

---

## 📌 Example Tickers

| Market | Example Tickers |
| ------ | --------------- |
| U.S. ETFs | `SPY`, `QQQ`, `IWM`, `DIA` |
| U.S. Stocks | `AAPL`, `MSFT`, `NVDA`, `TSLA`, `AMZN` |
| Index Symbols | `^SPX`, `^GSPC`, `^IXIC`, `^XSP` |
| Canadian Stocks | `SHOP.TO`, `TD.TO`, `RY.TO` |

Yahoo Finance ticker symbols can vary by exchange.

For 0DTE spread scanning, `SPY`, `QQQ`, and `IWM` are usually more reliable through `yfinance` than index symbols.

---

## ⚠️ Known Limitations

### yfinance Option Chains

The app uses `yfinance` for option-chain data.

Some index products may not return reliable option chains.

For example:

* `SPY` usually works well
* `QQQ` usually works well
* `IWM` usually works well
* `AAPL` usually works well
* `^XSP` may not return usable option expirations through `yfinance`

XSP options exist, but Yahoo/yfinance support may be inconsistent.

For XSP-specific scanning, a dedicated options data provider may be needed later.

Possible future providers:

* Cboe data
* Tradier
* Polygon
* ThetaData
* ORATS
* Broker API

---

### IV Rank Proxy

The app does not calculate true historical IV rank.

It uses a proxy based on current ATM IV compared with recent realized volatility.

---

### Greeks

Greeks are approximate and may differ from broker platform Greeks.

Broker Greeks may use:

* Real-time quote data
* Volatility surfaces
* Dividends
* Different interest rates
* American-style option adjustments

---

### 0DTE Risk

0DTE options carry major risks:

* Gamma risk
* Pin risk
* Sudden market movement
* Poor fills
* Fast theta/gamma changes
* News shock risk

Use this dashboard for paper trading only.

---

## ⚠️ Troubleshooting

### Missing Package Error

If the app shows an error like:

```bash
ModuleNotFoundError: No module named 'matplotlib'
```

Make sure your repo includes `requirements.txt` in the root directory and that it includes the missing package.

---

### No Option Chains Found

If the scanner says no option expirations were returned:

* Try a more liquid ticker such as `SPY` or `QQQ`.
* Make sure the ticker exists on Yahoo Finance.
* Disable 0DTE focus if no same-day expiration is available.
* Increase the DTE range.
* Use ETF symbols instead of index symbols when testing.

---

### Spread Scanner Is Empty

Try loosening filters:

* Increase max bid-ask spread %
* Lower minimum open interest
* Increase spread width range
* Increase DTE range
* Disable 0DTE-only focus if no same-day chain exists
* Test with `SPY` instead of `^XSP`

---

### App Is Slow on Streamlit Cloud

Try:

* Scanning fewer tickers
* Reducing max expirations per ticker
* Lowering max RSS articles
* Running FinBERT sentiment only when needed
* Testing with one ticker first

---

### FinBERT Sentiment Does Not Load

Make sure `requirements.txt` includes:

```txt
transformers
torch
feedparser
```

If the app is slow, lower the article count or avoid running the FinBERT tab unless needed.

---

## 📊 Output Screens

The app generates several outputs:

* Regime summary table
* Price chart
* Realized volatility chart
* Option chain table
* Approximate Greeks table
* Ranked spread scanner table
* Highlighted top final score
* Paper trade journal
* XGBoost ranker status
* Sentiment donut chart
* Article-level sentiment dropdowns
* News-to-spread readout
* Risk warnings

---

## 📚 Learning Objectives

This project demonstrates:

* Building an options analytics dashboard with Streamlit
* Fetching market data using Python
* Pulling and cleaning option chains
* Calculating approximate Greeks
* Building vertical spread candidates
* Combining credit spreads into iron condor candidates
* Supporting 0DTE scanning workflows
* Creating a rule-based spread scoring engine
* Using a paper-trade journal as labelled ML data
* Training an XGBoost classifier
* Applying FinBERT to financial news sentiment
* Translating news sentiment into options spread context
* Handling Streamlit Cloud deployment issues
* Communicating options model outputs through an interactive dashboard

---

## 🗺️ Future Improvements

Possible future upgrades:

* Dedicated XSP option-chain data provider
* Broker API connection for paper execution
* True historical IV rank
* Probability of touch
* Expected move calculation
* Opening range breakout filter
* VIX / VVIX / MOVE index inputs
* Gamma exposure filter
* Economic calendar integration
* Position sizing engine
* Backtesting engine
* More advanced ML ranking methods
* Saved model persistence
* Multi-day performance analytics

---

## ⚠️ Financial Disclaimer

This application is for **educational, research, and paper-trading purposes only**.

The scanner output, model scores, Greeks, sentiment scores, volatility classifications, news warnings, and spread candidates generated by this app should not be interpreted as financial advice, trading recommendations, or investment guidance.

Options are risky and can result in significant losses.

0DTE options are especially risky due to rapid gamma exposure, intraday volatility, and fast-changing liquidity.

Always verify data, pricing, Greeks, margin, and risk on your broker platform before making any real trading decisions.

---

## 👨‍💻 Author

**Woi-6ix**

Built as part of a machine learning, options analytics, financial modelling, natural language processing, and Streamlit dashboard development project.

---

## 📜 License

This project is licensed under the MIT License.

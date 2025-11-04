# Delta-Hedging with GARCH vs Black–Scholes

## Overview
This project investigates the effectiveness of delta-hedging strategies using two approaches:
- Black–Scholes model (BS): assumes constant volatility estimated from rolling windows of returns.
- GARCH model (Generalized Autoregressive Conditional Heteroskedasticity): estimates time-varying volatility dynamically from past returns.
  
The goal is to evaluate how well these models replicate the payoff of synthetic options across multiple tickers, strike prices, and maturities, while accounting for realistic trading frictions.

## Data
- Source: Yahoo Finance (yfinance) daily OHLC data.
- Tickers: AAPL, TSLA, F, HD, GM, SPY, QQQ
- Period: 2025-01-01 to 2025-10-11
- Returns: log returns used for volatility estimation and GARCH fitting.

## Methodology
### Option Setup
- Synthetic call options created with maturities of 21, 42, 63, 126 trading days.
- Strikes set at 0.9×, 1.0×, 1.1× spot price (ITM, ATM, OTM).
- Each contract lifecycle runs from start date until expiry.

### Hedging Simulation
Delta hedges computed daily using either BS or GARCH volatility forecasts.
Execution model includes:
- Commission per share
- Bid–ask spread cost
- Hysteresis band (to avoid tiny hedge trades)
- Max inventory limits

Cash account accrues interest at the risk-free rate.

When the hedge is closed at expiry, the option payoff is realized.

### Outputs Recorded
For each contract lifecycle:
- Initial price S0, strike K, expiry date
- Final NAV, option payoff, net P&L
- Hedging error = final NAV – payoff
- Fees paid, turnover (shares traded)
- Model details (BS window, GARCH p/q, etc.)
All results collected into the unified DataFrame df_liveish.

## Key Visualizations
- Net P&L density comparison between models.
- Cumulative Net P&L by Ticker – time series of strategy profitability.
- Boxplots of Net P&L by Maturity × Strike – sensitivity to contract design.
- Distribution of Fees as % of Gross P&L – highlights drag from transaction costs.
- Time Series of Hedging Errors – rolling replication error curves.
- Heatmap of Avg P&L by (Maturity × Strike Multiplier) – systematic patterns in hedge success.
- Distribution of hedging erros - shows the bias and variance of Black-Scholes vs GARCH
- Distribution of turnover per contract - which models were the quickest to purchase stocks
  
## Findings
- GARCH hedges routinely result in higher profit than Black-Scholes hedges.
- Short-dated, near-the-money options tend to hedge more cleanly.
- Black-Scholes models tend to accumulate more fees than GARCH.

## Limitations
- We generate synthetic call payoffs instead of using real listed option data removing the “market reality” of supply/demand for options
- We explicitly set the risk-free rate r = 0. While this simplifies formulas, in practice the risk-free rate shifts over time, and even small differences can affect discounting of payoffs or delta values, especially for long-dated options.
- We define strikes as multiples of S_0 at inception (ATM = 1.0, ITM = 0.9, OTM = 1.1). In real markets strikes are discrete and option chains have irregular availability depending on the underlying. Our method gives systematic ITM/ATM/OTM groupings, but doesn’t reflect how traders pick strikes.
- We only use around 10 months of data for a couple of tickers. This restricts the robustness of Our findings, especially for volatility models like GARCH, which benefit from longer series to capture volatility regimes. Also, using a short window makes it hard to generalize across bull/bear/sideways markets.
- The execution model is simplified compared to real-world microstructure. To be more accurate, in the future this project will need to take into account partial fills, order book depth and volatility-driven spread widening.
- Each option lifecycle is simulated with fixed maturities, when in practice traders roll options or trade across a wide spectrum of expiries. This might bias results toward certain behaviors.
- The results might be driven by the quirks of our chosen assets, for example TSLA’s extreme volatility & Ford’s stability. Broader coverage of assets could validate the conclusions better.

## Next Steps
- Extend to intraday data (higher hedge frequency).
- Add alternative volatility models (EGARCH, Heston).
- Incorporate real option chain data to compare against market-implied volatilities.
- Larger window of data and more assets.
- Make Execution Model more realistic.

## How to Run
Clone repo and install requirements: pip install -r requirements.txt
Run the Jupyter notebook to:
- Download data
- Fit BS and GARCH models
- Simulate hedges
- Generate performance metrics and plots
  
## Skills Demonstrated
- Time series modeling (rolling vols, GARCH)
- Options pricing and hedging
- Backtesting with realistic execution costs
- Data visualization and performance analysis
  
## Python libraries:
pandas, numpy, arch, matplotlib, seaborn, yfinance

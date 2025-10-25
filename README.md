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
  
## Findings (Illustrative)
- BS hedges often show bias when volatility shifts sharply.
- GARCH hedges adapt better to changing volatility but may suffer higher turnover and costs.
- Short-dated, near-the-money options tend to hedge more cleanly.
- Transaction costs can erase theoretical edge unless carefully managed.

## Next Steps
- Extend to intraday data (higher hedge frequency).
- Add alternative volatility models (EGARCH, Heston).
- Incorporate real option chain data to compare against market-implied volatilities.

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

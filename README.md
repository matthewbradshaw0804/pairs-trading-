# Pairs Trading — IUSB / JCPB Bond ETFs

A cointegration-validated statistical arbitrage strategy on two bond ETFs
(IUSB and JCPB), using a static OLS hedge ratio to construct a spread and
a rolling z-score of that spread to generate trading signals.

## Methodology

- **Universe:** IUSB (iShares Core Total USD Bond Market ETF) and JCPB
  (JPMorgan Core Plus Bond ETF)
- **Cointegration validation:** Engle-Granger (AEG) test confirms the pair
  is cointegrated before trading it
- **Hedge ratio:** Static OLS regression of IUSB on JCPB, fit once over the
  full lookback window; the resulting beta defines the spread:
  `spread = IUSB - beta * JCPB`
- **Signal:** Rolling 60-day z-score of the spread
  - Enter short spread when z-score > +2
  - Enter long spread when z-score < -2
  - Exit to flat when |z-score| < 0.5
  - Otherwise, carry the existing position forward
- **Backtest:** Position sized in dollar-notional terms ($10,000), with
  transaction costs (5bps per leg) applied on every position change and
  expressed in the same return-space units as PnL. Performance evaluated
  via Sharpe ratio, max drawdown, and win rate, with results segmented by
  period to check for regime dependence.

## Results

| Period | Total PnL | Trades | Losing Trades | Win Rate |
|---|---|---|---|---|
| 2022-01-01 to 2023-12-31 | 0.02 | 10 | 5 | 50.00% |
| 2024-01-01 to 2026-08-06 | -0.01 | 45 | 32 | 30.43% |

**Sharpe Ratio:** 0.07 · **Max Drawdown:** 0.02 · **Win Rate** 49.18%

*![Equity Curve](./assets/equity_curve.png)*

Performance is close to flat overall, with a clear decline in win rate
between the two periods — consistent with a broader edge decay in this
pair during the post-rate-hike low-volatility bond regime. See the
[Kalman Filter Pairs Trading](https://github.com/YOUR_USERNAME/kalman-filter-pairs)
repo for a dynamic hedge-ratio extension of this same strategy, tested
under identical cost assumptions for direct comparison.

## Stack

Python · pandas · NumPy · statsmodels · yfinance · matplotlib

## Run it

\```bash
pip install -r requirements.txt
python pairs_trading.py
\```

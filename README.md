# Trading a cross-border price gap

Two notebooks on one question from the J.P. Morgan Systematic Trading Challenge.

| Notebook | What it is |
|---|---|
| `JP Morgan Systematic Trading Project.ipynb` | The model and the trading rule. October 2025, my first term at Imperial College London. |
| `Backtest.ipynb` | A stationarity test and a backtest of that rule. Added in November 2026. |

## The question

The same good is priced in two markets and two currencies: "German apples" in euros and "UK apples" in pounds, with the daily EUR/GBP rate alongside. The series are daily from January 2015 to August 2023, 2,250 observations.

Is the gap between the two prices a mean-reverting spread, and if it is, how would you trade it?

## The model and the rule (October 2025)

1. **Common currency.** Converts the UK price to euros with the daily exchange rate.
2. **Hedge ratio.** Regresses the UK price on the German price by OLS: UK = −41.14 + 1.689 × German. The residual is the basis.
3. **Mean reversion.** Fits an Ornstein–Uhlenbeck process to the basis through an AR(1) regression. The reversion speed is θ = 3.31 a year, a half-life of ln 2 / θ, or about 53 trading days. The long-run mean is μ = 2.57 and the volatility σ = 85.7.
4. **Predictor.** Fits a linear model of the German price on the UK price (R² of 0.93).
5. **Trading rule.** Sets out a z-score rule on the basis: enter when it is more than 2 standard deviations from its mean, exit inside 0.5, stop out beyond 3, with positions sized by the hedge ratio.

## The test (November 2026)

The copy of the first notebook in this repository sets out the rule without testing it. `Backtest.ipynb` tests it on the same data. No trading costs are included.

| Question | Answer |
|---|---|
| Is the basis stationary? | No. Augmented Dickey–Fuller p-value 0.95, Engle–Granger p-value 0.98 |
| The rule with the original full-history parameters | 2 trades in eight and a half years |
| Out of sample, trading at the close that produced the signal | Sharpe ratio between −0.32 and 1.01, depending on how much history the parameters are estimated from |
| Out of sample, trading one day after the signal | Sharpe ratio between −0.48 and 0.37 |

The out-of-sample tests all trade the second half of the data, April 2019 to August 2023: one with parameters frozen from the first half, and eight walk-forward settings.

The results that look good come from one-day noise. Daily changes in the basis have an autocorrelation of −0.48, so the rule is often triggered by a spike that is gone the next day, and a backtest that fills the order at the spike collects the reversal. Trade one day later and nothing stands clear of zero. I would not trade the rule as written.

## What changed in the first notebook

In November 2026 I corrected the labels in Question 3, which had compared the fitted German price with the UK price, and re-ran that one cell. I also pointed the data path at `data/`. Nothing else in the notebook changed.

## Run it

The dataset came with the challenge and is not included. Save it as `data/Apples Data Set.csv`, then:

```bash
pip install -r requirements.txt
jupyter notebook
```

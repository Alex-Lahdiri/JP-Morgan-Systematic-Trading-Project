# Trading a cross-border price gap

One notebook, written for the J.P. Morgan Systematic Trading Challenge in October 2025, my first term at Imperial College London.

## The question

The same good is priced in two markets and two currencies: "German apples" in euros and "UK apples" in pounds, with the daily EUR/GBP rate alongside. The series are daily from January 2015, 2,250 observations.

Is the gap between the two prices a mean-reverting spread, and if it is, how would you trade it?

## What the notebook does

1. **Common currency.** Converts the UK price to euros with the daily exchange rate.
2. **Hedge ratio.** Regresses the UK price on the German price by OLS: UK = −41.14 + 1.689 × German. The residual is the basis.
3. **Mean reversion.** Fits an Ornstein–Uhlenbeck process to the basis through an AR(1) regression. The reversion speed is θ = 3.31 a year, a half-life of ln 2 / θ, or about 53 trading days. The long-run mean is μ = 2.57 and the volatility σ = 85.7.
4. **Predictor.** Fits a linear model between the two price series.
5. **Trading rule.** Sets out a z-score rule on the basis: enter when it is more than 2 standard deviations from its mean, exit inside 0.5, stop out beyond 3, with positions sized by the hedge ratio.

## What is not in this copy

This copy of the notebook stops at the model and the trading rule. It contains no backtest and no stationarity test. The hedge ratio and the OU parameters are fitted on the full history, so every number above is in-sample.

The first chart also shows the basis widening over the last part of the sample. A single fixed hedge ratio does not capture that, so the fit needs checking on rolling windows before the rule is trusted.

## Run it

The dataset came with the challenge and is not included. Save it as `data/Apples Data Set.csv`, then:

```bash
pip install -r requirements.txt
jupyter notebook "JP Morgan Systematic Trading Project.ipynb"
```

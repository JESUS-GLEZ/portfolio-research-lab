# Portfolio strategies and comparison modes

Portfolio Research Lab lets you test investment ideas by changing the return
forecast, the rules for building a portfolio, or the algorithm that searches for
candidate portfolios. In **New Run**, choose the comparison that matches the
question you want to answer.

## Strategies

**Buy and Hold** invests in one asset and holds it through the backtest. Use an
asset such as SPY as a baseline under the same dates and trading assumptions as
the other strategies. An SPY baseline built from Alpaca's split-adjusted equity
bars measures price performance before dividends.

**Momentum Risk Parity** ranks assets by trailing returns. It applies a trend
filter based on each asset's moving average, then gives the selected assets
weights proportional to the inverse of their historical volatility. If the
trend filter removes every asset, the strategy falls back to the momentum
ranking. This weighting rule uses each asset's volatility on its own.

**Optimization** estimates expected returns from past prices and uses a
covariance estimate to model how assets move together. The default return
estimator is an exponentially weighted mean with a span of six periods. You can
try a longer span or blend it with the historical mean. An optional news
shortlist changes which assets enter the search.

**Sentiment EWM** adjusts the exponentially weighted return forecast using
news sentiment. For each asset, a regression fitted on past observations
estimates how sentiment changes the forecast. When there is too little history
to fit the model, the default fallback is the unadjusted exponentially
weighted forecast. The resulting forecasts feed the portfolio optimizer.

**Combined** uses a ridge-regularized VARX model to forecast the next period's
returns from lagged returns and news sentiment. It then uses those forecasts
to build a portfolio with the optimizer.

### How the optimizer builds a portfolio

The optimizer searches for long-only portfolios within the chosen range of
holdings. It looks for trade-offs among expected return, estimated variance
and concentration. The **Optimization** strategy lets you balance capital
weights, risk contributions, or both. Capital weights measure the share of
money in each asset. Risk contributions measure each holding's share of
estimated portfolio variance, accounting for how holdings move together.
Two holdings with the same weight can contribute very different amounts of risk.

The search produces a set of candidate portfolios. The selection rule then
picks one at each rebalance. **Maximum Sharpe ratio** favors forecast return per
unit of estimated volatility. **Higher return within a volatility budget**
selects the highest expected return within a limit relative to the volatility
of the Sharpe candidate. **Minimum risk at a return target** sets a threshold
between the expected returns of the minimum-variance and highest-return
candidates found. It then selects the lowest-variance candidate that meets it.

## Comparison modes

### Backtest investment strategies

Use this to see how complete portfolio rules would have behaved over the same
historical period. Each strategy uses information available by the decision
cutoff. The simulator uses opening prices for trades, charges the configured
fee on buys and sells, and values positions at period end. Compare returns and
drawdowns alongside holdings, turnover and costs to see what drove the result.

For example, start with the [monthly backtest](../examples/README.md#small-monthly-backtest-in-the-app)
and add a second **Optimization** configuration. Change **Portfolio balance**
from **Balanced risk contributions** to **Balanced capital weights** while
keeping the return estimator, holdings limits, selection rule and search
settings the same. Compare the holdings at the same rebalances, then check
how the differences show up in trading costs and drawdown. If you use one
period to choose a configuration, reserve a later period to evaluate it.

### Compare return predictors

Use this to test the forecasts themselves. In a walk-forward evaluation, each
predictor uses the history available at the time and forecasts next-period
returns. The app scores the forecasts against observed returns on a common
set of dates and assets. For example, compare an exponentially weighted mean
with a sentiment-based predictor and check both the size of the errors and
how well each model ranks assets. This mode measures prediction accuracy.
Use a strategy backtest to see how a forecast affects holdings and returns
after trading costs.

### Benchmark portfolio optimization solvers

Use this to compare how search algorithms solve the same portfolio problem.
The benchmark uses expected returns and covariance from the final estimation
window and shares the model constraints across solvers. Compare the sets of
risk–return trade-offs found and the time each solver took. Match the search
budgets when you want to assess the effect of changing the solver itself.

The optional convex mean–variance frontier provides a reference with relaxed
balance and holdings-count constraints. The
[offline optimization example](../examples/README.md#offline-optimisation-example)
uses synthetic inputs to illustrate the portfolio search and its outputs.

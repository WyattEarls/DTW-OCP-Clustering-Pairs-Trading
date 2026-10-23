# DTW-OCP-Clustering-Pairs-Trading

A rolling-window backtest testing whether Dynamic Time Warping (DTW) and Optimal Causal Path (OCP) distances select better stock pairs than the classic distance method of Gatev, Goetzmann and Rouwenhorst (2006). The study covers S&P 500 stocks from 2001 to 2025.

## Research Question

Does Clustering stocks before selecting pairs improve the Gatev distance method, and if so, does any improvement come from the clustering itself, from allowing time warping or from imposing a causal ordering?

Each step was tested with one planned comparison, specified before any results were computed:

1. Clustered Manhattan vs. Gatev: does clustering help?
2. Clustered DTW vs clustered Manhattan: Does time warping help?
3. Clustered OCP vs Clustered DTW: does restricting warping to causal paths help?

## Design

| Element | Choice |
| --- | --- |
| Universe | Point-in-time S&P 500 membership at each formation start; 221 to 481 usable stocks per window |
| Windows  | 50 rolling windows: form pairs on 12 months, trade the next 6, step forward 6 months (trading January 2001 to December 2025)|
| Input | Cumulative returns over the formation year |
| Distances | Manhattan, DTW and OCP, all with absolute-difference cost; OCP restricts alignment paths to one stock leading the other |
| Clustering | Ward hierarchical clustering; k in {2, 4, 7} chosen per window by majority vote of silhouette, Calinski-Harabasz and Davies-Bouldin |
| Pair selection | Top 20 pairs within clusters by each method's own distance; Gatev selects its top 20 by sum of squared differences across the whole universe |
| Trading rule | Identical for all models: open at 2 formation-period standard deviations, close when prices cross, one-day execution lag |
| Costs | 0, 5, 10 and 20 basis points per dollar traded |
| Tests | Block bootstrap of Sharpe differences (5,000 resamples, 20-day blocks); alpha from a market regression with Newey-West standard errors |

## Results

**Performance, 2001 to 2025**

| Model | Sharpe (0 bps) | Sharpe (10 bps) | Annual return (0 bps) | Annual volatility | Max Drawdown (log) |
|---|---|---|---|---|---|
| Clustered DTW | 0.219 | 0.006 | 0.52% | 2.36% | -0.108 |
| Gatev distance | 0.211 | -0.016 | 0.49% | 2.34% | -0.092 |
| Clustered Manhattan | 0.056 | -0.156 | 0.14% | 2.46% | -0.152 |
| Clustered OCP | 0.051 | -0.146 | 0.12% | 2.41% | -0.129 |
| S&P 500 buy-and-hold (context) | 0.352 | 8.47% | 19.27% | -0.804 |

**Planned comparisons (0 bps)**

| Comparison | Sharpe difference | 95% bootstrap interval | p-value |
|---|---|---|---|
| Clustered Manhattan vs Gatev | -0.155 | -0.323 to 0.013 | 0.068 |
| Clustered DTW bs clustered Manhattan | +0.163 | -0.158 to 0.477 | 0.326 |
| Clustered OCP vs clustered DTW | -0.168 | -0.428 to 0.084 | 0.174 |

**Main findings**

- No planned comparison was statistically significant at any cost level.
- No model earned a Sharpe ratio or alpha significantly above zero. At 20 bps, clustered Manhattan and OCP had significantly negative alphas.
- Every model's edge disappeared at 10 bps of transaction costs.
- The clustering step had almost no effect: the three indices chose two clusters in all 150 decisions, and the cluster restriction changed under 1% of selected pairs. The comparisons therefore test the distance measures themselves.
- DTW and Gatev chose substantially different pairs (10.8 of 20 shared per window on average) yet performed no differently.
- Market betas were about 0.03, consistent with market-neutral strategies.
- Gains came mainly from 2001 to 2009; returns after 2010 were close to zero, consistent with the literature on declining pairs-trading profits.

## Limitations

- **Survivorship:** Yahoo Finance does not retain prices for most delisted companies, so early windows draw from a smaller universe tilted toward survivors. CRSP data would remove this limitation.
- **Reused tickers:** tickers reassigned to other companies were detected using delisted markers available through 2019; reuse after 2019 could not be detected.
- **Costs:** short-borrow fees and margin requirements are not modeled.
- **Scope:** results cover S&P 500 large caps at daily frequency only.

## Earlier work

Earlier versions of this project selected pairs once and traded them for up to 17 years. Those versions contained errors in how returns were measured, how the risk-free rate was applied and how benchmark returns and transaction costs were calculated. The errors were identified and corrected, and the rolling-window study above replaced that design. Earlier notebooks are kept in 'notebooks/archive/' for reference.

## Repository structure

'''
notebooks/
  Rolling-DTW-OCP-Manhattan-vs-Gatev-SP500.ipynb  final study
  archive/                                          earlier notebooks
results/        result tables (CSV) and figures
data/           frozen membership files

## How to run

'''
pip install -r requirements.txt
'''

Open the final notebook and run all cells. The first run downloads daily prices from Yahoo Finance and computes all 50 windows, which takes roughly 25 minutes. Every window's results are cached, so later runs take seconds.

## References

Gatev, E., Goetzmann, W. N., & Rouwenhorst, K. G. (2006). Pairs trading: Performance of a relative-value arbitrage rule. *The review of Financial studies, 19*(3), 797-827. https://doi.org/10.1093/rfs/hhj020

Sakoe, H., & Chiba, S. (1978). Dynamic programming algorithm optimization for spoken word recognition. *IEEE Transactions on Acoustics, Speech, and Signal Processing, 26*(1), 43-49. https://doi.org/10.1109/TASSP.1978.1163055

Stübinger, J. (2019). Statistical arbitrage with optimal causal paths on high-frequency data of the S&P 500. *Quantitative Finance, 19*(6), 921-935. https://doi.org/10.1080/14697688.2018.1537503

## Author

Wyatt Earls, Math of Data & Computing, UTSA Honors College. Research conducted as a research assistant under Professor Donald Lien.

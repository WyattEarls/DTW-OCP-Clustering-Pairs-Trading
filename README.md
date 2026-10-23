# DTW-OCP-Clustering-Pairs-Trading

A rolling-window backtest testing whether Dynamic Time Warping (DTW) and Optimal Causal Path (OCP) distances select better stock pairs than the classic distance method of Gatev, Goetzmann and Rouwenhorst (2006). The study covers S&P 500 stocks from 2001 to 2025.

## Research Question

Does Clustering stocks before selecting pairs improve the Gatev distance method, and if so, does any improvement come from the clustering itself, from allowing time warping or from imposing a causal ordering?

Each step was tested with one planned comparison, specified before any results were computed:

1. Clustered Manhattan vs. Gatev: does clustering help?
2. Clustered DTW vs clustered Manhattan: Does time warping help?
3. Clustered OCP vs Clustered DTW: does restricting warping to causal paths help?








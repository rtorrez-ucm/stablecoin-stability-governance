# Analysis of Centralized and Decentralized Stablecoins: A Comparative Study of USDT and DAI

This repository contains the code used for the implementation of the Master’s Thesis (TFM) at the Faculty of Computer Science, Complutense University of Madrid (UCM).

## Project Overview
The project analyzes the **stability** of two major fiat-pegged stablecoins—**USDT** (centralized) and **DAI** (decentralized)—and documents governance differences in the written thesis.  
The computational pipeline (in a single Jupyter Notebook) downloads daily market data, validates it, builds three measures of stability/activity (M1–M3), produces figures and tables, and estimates **VAR** models with orthogonalized IRFs for robustness and comparability with prior literature.

- **Window:** 2020-05-01 → 2025-05-01 (UTC)  
- **Assets:** USDT-USD, DAI-USD, BTC-USD  
- **Measures:**  
  - **M1** – deviation from the $1 peg: `CLOSE − 1`  
  - **M2** – realized volatility (Parkinson): `sqrt((ln(H)−ln(L))^2/(4 ln 2))`  
  - **M3** – `TOTAL_INDEX_UPDATES` (microstructure activity proxy)  
- **VAR panels (Cholesky, BTC first):**  
  - VAR(M1) = [Bitcoin_Volatility, USDT_Measure1, DAI_Measure1]  
  - VAR(M2) = [Bitcoin_Volatility, USDT_Measure2, DAI_Measure2]  
- **Stationarity:** ADF (`autolag='AIC'`); estimate in levels if stationary, otherwise first differences  
- **Specs saved:** (A) fixed p=5 (paper comparability) and (B) p selected by AIC (robustness)


## Dataset Collection
Data are obtained programmatically from the **CoinDesk Data API** (`index/cc/v1/historical/days`) using the notebook in this repository. Parameters:
- `market=cadli`, `aggregate=1`, `fill=true`, `apply_mapping=true`, daily granularity
- Strict validation: positive OHLC, `LOW ≤ HIGH`, chronological order, drop duplicates, basic gap reporting.

If you prefer to run fully offline, the notebook also exports **snapshots** of the processed per-asset series to `/snapshots`.

## Files in this Repository
- `LICENSE` — MIT license.  
- `README.md` — this file.  
- `stablecoin_analysis_usdt_dai.ipynb` — **main analysis notebook**.  
- `figs/` — time-series plots, histograms, boxplots.  
- `irfs/` — orthogonalized impulse-response plots (paper p=5 and robustness p=AIC).  
- `models/` — plain-text summaries of fitted VAR models.  
- `snapshots/` — validated CSV snapshots per asset (post-validation).  
- `tables/` — CSV outputs (descriptives, correlations, ADF reports, lag-selection ICs, IRF arrays, panels, etc.).

## Instructions to Replicate the Work

### 1) Set up the environment
```bash
python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

**Suggested `requirements.txt`:**
```
python>=3.10
requests
pandas
numpy
matplotlib
scipy
statsmodels
```

### 2) Configure the API key
Create an environment variable:
```bash
export COINDESK_API_KEY="your_api_key_here"   # Windows (PowerShell):
# $Env:COINDESK_API_KEY="your_api_key_here"
```
In the notebook, the key is read with:
```python
api_key = os.getenv("COINDESK_API_KEY")
```

### 3) Run the notebook
```bash
jupyter lab   # o jupyter notebook
```
Open `stablecoin_analysis_usdt_dai.ipynb` and **Run All**.  
The script will create (if missing) the folders `figs/`, `irfs/`, `tables/`, `models/`, `snapshots/` and populate them with outputs.

### 4) Expected outputs
- **Figures (`/figs`)**: `m1_timeseries_usdt_dai.png`, `m2_timeseries_usdt_dai.png`, `m3_timeseries_usdt_dai.png`, `m3_boxplot_usdt_vs_dai.png`, `m3_hist_usdt_dai.png`.
- **Tables (`/tables`)**: 
  - `descriptive_stats_m1_m2_m3_usdt_dai.csv`, `metrics_7_1.csv`  
  - `extremes_m2_top3.csv`, `m2_counts_over_thresholds.csv`  
  - `correlation_matrix_m1_m2_m3_usdt_dai.csv`, `correlations_pairs_with_pvalues.csv`  
  - `adf_M1_raw.csv`, `adf_M2_raw.csv`, `adf_M1_levels|diff.csv`, `adf_M2_levels|diff.csv`  
  - `lag_selection_M1_max*.csv`, `lag_selection_M2_max*.csv`  
  - `var_panel_M1_raw.csv`, `var_panel_M1_levels|diff.csv`  
  - `var_panel_M2_raw.csv`, `var_panel_M2_levels|diff.csv`  
  - `irf_values_M1_*.csv`, `irf_values_M2_*.csv`
- **Models (`/models`)**: `var_M1_paper_p5.txt`, `var_M1_aic_p*.txt`, `var_M2_paper_p5.txt`, `var_M2_aic_p*.txt`.
- **IRFs (`/irfs`)**: `irf_M1_paper_p5.png`, `irf_M1_aic_p*.png`, `irf_M2_paper_p5.png`, `irf_M2_aic_p*.png`.
- **Snapshots (`/snapshots`)**: `df_usdt.csv`, `df_dai.csv`, `df_bitcoin.csv`.

## Notes on Reproducibility
- All timestamps are handled in **UTC**.  
- The pipeline enforces daily frequency and drops irregularities before estimation.  
- BTC volatility is ordered first for Cholesky identification.  
- A fixed **p=5** model is estimated for comparability with the reference paper; **AIC** selection is added for robustness.

## Research context & references
This repository accompanies the Master’s Thesis:  
*“Analysis of Stability and Governance in Centralized and Decentralized Stablecoins: A Comparative Study of USDT and DAI”* (UCM, 2025).  

The empirical pipeline is adapted from Thanh et al. (2022), *Are the stabilities of stablecoins connected?*, with modifications to focus on USDT vs DAI and to integrate governance discussion in the written thesis.  

## License
This project is released under the **MIT License**. See [`LICENSE`](./LICENSE).

## Contact
For questions about replication or artifacts, please open an issue in this repository.

## Author
Rommel Torrez Huanca, Master’s Thesis (TFM), Complutense University of Madrid, 2025.


# FIN 585: Net Share Issuance and Future Stock Returns

This repository documents a Finance 585 project that evaluates portfolio performance sorted on **36-month Net Share Issuance** (share growth). The analysis examines the cross-sectional relationship between past share issuance and subsequent stock returns using CRSP monthly stock data, Compustat accounting data, and Fama-French factor returns.

The full analysis was completed on Google Colab. This repo contains the cleaned notebook and methodology for reference.

## Presentation

**https://docs.google.com/presentation/d/1RwUPysZtQUqKRcb4VmFTo7juwcPH9zATGS_WRcQKJys/edit?slide=id.g3d5fb476999_4_358#slide=id.g3d5fb476999_4_358** — results, tables, and visualizations from the completed analysis.

> Replace the link above with your Google Slides or PDF URL.

## Overview

Net share issuance measures how much a firm's shares outstanding have grown over a three-year window. Stocks are grouped into portfolios based on this signal, and portfolio returns are evaluated in-sample (1975–1984) and out-of-sample (1985–present). Results are summarized with **Carhart 4-Factor** and **CAPM** regressions, publication-style tables (`great_tables`), and **Altair** visualizations.

### Data Sources

| Source | Description |
|--------|-------------|
| **CRSP** | Monthly stock data: returns (`RET`), shares outstanding (`SHROUT`), delisting returns (`DLRET`), value-weighted market return (`vwretd`), and share-adjustment factors |
| **Compustat** | Point-in-time accounting data merged to CRSP via `PERMNO` |
| **Fama-French factors** | Daily factor returns (market, size, value, momentum, risk-free rate) compounded to monthly frequency |

> **Note:** CRSP and Compustat data are proprietary and are not included in this repository.

## Methodology

### Share Growth (Net Share Issuance)

1. Adjust shares outstanding for splits and other corporate actions:  
   `adj_shrs = SHROUT × CFACSHR`
2. Compute 36-month share growth for each stock-month:  
   `shrs_growth = adj_shrs_t / adj_shrs_{t-36}`  
   Stocks without 36 months of history receive `NaN`.

### Portfolio Sorting

Each month, stocks with valid share-growth and accounting data are sorted into four bins using `pd.qcut` with breakpoints at the 20th, 60th, and 80th percentiles:

| Portfolio | Share Growth Percentile |
|-----------|-------------------------|
| `0-20` | Lowest issuance (bottom 20%) |
| `20-60` | 20th–60th percentile |
| `60-80` | 60th–80th percentile |
| `80-100` | Highest issuance (top 20%) |

A **spread** portfolio (high minus low issuance) is also constructed. Delisting returns are incorporated to mitigate survivorship bias.

### Performance Evaluation

- Portfolio returns are merged with monthly Fama-French factors.
- **Carhart 4-Factor model:** excess return regressed on market, SMB, HML, and UMD.
- **CAPM:** excess return regressed on the market factor only.
- Regressions are run separately for in-sample and out-of-sample periods for each portfolio bin and the spread.

## Notebook (Optional)

`FIN_585_project.ipynb` is the refactored analysis notebook. To re-run it locally you would need your own CRSP and Compustat CSV extracts in a `data/` directory (`crsp_data.csv`, `comp_data.csv`). Fama-French factors are fetched automatically from [diether.org](https://diether.org/prephd/08-factors.csv).

```bash
pip install -r requirements.txt
jupyter notebook FIN_585_project.ipynb
```

## Project Structure

```
├── FIN_585_project.ipynb   # Analysis notebook (reference)
├── requirements.txt        # Python dependencies
├── README.md
└── .gitignore
```

## License

Academic use only. CRSP and Compustat data remain subject to their respective vendor licenses.

# Profit-Aware Pricing in Two-Sided Marketplaces

Replication code for the working paper: **"Profit-Aware Pricing in Two-Sided Marketplaces: Demand Elasticity, Cost Uncertainty, and Customer Lifetime Value in Brazilian E-Commerce"**

**Author:** Dinesh R. Poddaturi, Ph.D.  
**Paper:** [SSRN — ssrn.com/abstract=6502262](https://ssrn.com/abstract=6502262)  
**Website:** [dineshpoddaturi.github.io](https://dineshpoddaturi.github.io)  
**Status:** Complete

---

## Overview

This repository contains the Python analysis underlying the working paper. The analysis applies to 110,840 transactions across 71 product categories from the Olist Brazilian e-commerce marketplace (2016–2018), developing an integrated pricing framework that combines demand elasticity estimation, profit optimization under cost uncertainty, customer lifetime value modeling, and sequential purchase pattern analysis.

The paper demonstrates that revenue maximization and profit maximization are fundamentally misaligned in elastic markets, and that cost uncertainty dominates elasticity precision in practical pricing decisions.

---

## Key Findings

- A revenue-maximizing strategy for electronics recommends a 40% price cut generating 82.7% revenue but -144% profit at a 65% COGS structure.
- A 5-point COGS error forfeits 70–75% of potential profit gains. A 20% elasticity error preserves profitability across all scenarios. Cost validation matters more than elasticity precision.
- Electronics is structurally underpriced: profit-optimal pricing recommends a 20% price increase despite elastic demand (-2.18), yielding +5.6% profit and +BRL 3,143/month.
- Same-order bundling is non-viable (0% co-purchase rate across focus categories). Sequential recommendations triggered at days 21, 29, and 56 post-purchase capture the majority of repeat customers within their natural return window.
- Freight costs affect repeat purchase probability at approximately the same magnitude as product price (coefficients -0.076 and -0.079 respectively).

---

## Repository Structure

```
ecommerce-pricing-transformation/
├── README.md
├── requirements.txt
├── .gitignore
└── notebooks/
    ├── 01_data_exploration_business_context.ipynb
    ├── 02_segmentation_elasticity_clv.ipynb
    ├── 03_sellercompetition_dynamics_platform_revenue.ipynb
    ├── 04_bundle_analysis_notapplicable.ipynb
    └── 05_optimization_scenario_simulation.ipynb
```

---

## Notebooks

| Notebook | Description |
|---|---|
| 01_data_exploration_business_context | Data loading, cleaning, product bucket construction, repeat purchase analysis, dataset preparation |
| 02_segmentation_elasticity_clv | Price elasticity estimation, propensity modeling, customer lifetime value analysis |
| 03_sellercompetition_dynamics_platform_revenue | Seller concentration analysis, geographic pricing patterns, hub-and-spoke distribution structure, freight as demand barrier |
| 04_bundle_analysis_notapplicable | Bundle opportunity analysis — co-purchase rates, cross-category affinity (null result: bundling not viable) |
| 05_optimization_scenario_simulation | Profit-aware pricing optimization, cost sensitivity analysis, scenario simulation, sequential purchase recommendation framework |

---

## Data Source

**Brazilian E-Commerce Public Dataset by Olist**  
Available at: [https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)

### Required Files

Download the following files from Kaggle and place them in a `data/` directory at the repo root:

- `olist_orders_dataset.csv`
- `olist_order_items_dataset.csv`
- `olist_products_dataset.csv`
- `olist_sellers_dataset.csv`
- `olist_customers_dataset.csv`
- `olist_order_reviews_dataset.csv`
- `olist_geolocation_dataset.csv`
- `olist_order_payments_dataset.csv`
- `product_category_name_translation.csv`

Data files are not tracked in this repository. Approximately 150MB uncompressed.

---

## Requirements

Python 3.9+. Install dependencies with:

```bash
pip install -r requirements.txt
```

Core packages: pandas, numpy, scipy, statsmodels, scikit-learn, matplotlib, seaborn

---

## Reproducing the Analysis

### 1. Clone the repository

```bash
git clone https://github.com/DineshPoddaturi/ecommerce-pricing-transformation.git
cd ecommerce-pricing-transformation
```

### 2. Set up environment

```bash
python -m venv venv
source venv/bin/activate  # On Mac/Linux
pip install -r requirements.txt
```

### 3. Download the data

Download the Olist dataset from Kaggle and place all CSV files in the `data/` directory.

### 4. Run the notebooks

Notebooks 01, 02, and 05 are sequential — each depends on outputs from the previous. Notebooks 03 and 04 load raw CSV files directly and can be run independently.

```
01 -> 02 -> 05   (sequential)
03              (independent)
04              (independent)
```

---

## Citation

If you use this code or analysis, please cite:

```
Poddaturi, D.R. (2026). Profit-Aware Pricing in Two-Sided Marketplaces:
Demand Elasticity, Cost Uncertainty, and Customer Lifetime Value in
Brazilian E-Commerce. Working paper. Available at SSRN:
https://ssrn.com/abstract=6502262
```

---

## Author

**Dinesh R. Poddaturi, Ph.D.**  
Applied Economist | Pricing Strategy & Marketplace Economics  
[LinkedIn](https://www.linkedin.com/in/dinesh-r-poddaturi) | [Website](https://dineshpoddaturi.github.io) | [SSRN](https://ssrn.com/author=10964128)

---

## License

MIT License. See LICENSE file for details.
# E-Commerce Marketplace Pricing Transformation

**Strategic pricing analysis combining customer segmentation, demand elasticity, and CLV optimization**

## Project Overview

Analysis of 100K+ transactions from a Brazilian e-commerce marketplace (Olist) to develop data-driven pricing strategies that balance revenue growth, customer retention, and profitability.

### Business Problem
- High price variance for identical products (seller competition)
- Low customer repeat purchase rates
- Margin pressure from aggressive pricing
- Suboptimal platform commission structure

### Approach
- Customer segmentation (RFM analysis + clustering)
- Price elasticity estimation (discrete choice modeling)
- Marketplace dynamics analysis (two-sided market)
- Customer lifetime value optimization
- Multi-objective pricing framework

### Expected Impact
- 12-15% GMV increase
- 20% customer lifetime value improvement
- 3-5% margin expansion

## Project Structure
```
ecommerce-pricing-transformation/
├── README.md
├── requirements.txt
├── data/                          # Raw data (not committed)
├── notebooks/                     # Jupyter notebooks
│   ├── 01_data_exploration_business_context.ipynb
│   ├── 02_segmentation_elasticity_clv.ipynb
│   ├── 03_seller_marketplace_dynamics.ipynb
│   └── 04_optimization_clv_scenarios.ipynb
├── src/                           # Reusable Python functions
│   ├── data_processing.py
│   ├── modeling.py
│   └── visualization.py
└── outputs/                       # Results, figures, reports
    ├── executive_deck.pdf
    ├── technical_report.pdf
    └── figures/
```

## Data Source

Brazilian E-Commerce Public Dataset by Olist

**Kaggle:** https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce

### Files Required

Download the following files from Kaggle and place them in this folder:

- `olist_orders_dataset.csv`
- `olist_order_items_dataset.csv`
- `olist_products_dataset.csv`
- `olist_sellers_dataset.csv`
- `olist_customers_dataset.csv`
- `olist_order_reviews_dataset.csv`
- `olist_geolocation_dataset.csv`
- `olist_order_payments_dataset.csv`
- `product_category_name_translation.csv`

## Data Size

Approximately 150MB uncompressed

## Setup
```bash
# Download from Kaggle
# Extract zip file
# Place all .csv files in this folder
```

**Note:** Data files are not tracked in Git (see `.gitignore`)

## Technologies

- **Python 3.9+**
- **Analysis:** pandas, numpy, scipy
- **Modeling:** statsmodels, scikit-learn
- **Visualization:** matplotlib, seaborn, plotly
- **Dashboard:** Power BI

## Getting Started
```bash
# Clone repository
git clone https://github.com/DineshPoddaturi/ecommerce-pricing-transformation.git

# Install dependencies
pip install -r requirements.txt

# Download data from Kaggle and place in data/ folder
```

## Project Status

🚧 **In Progress** - Week 1: Data exploration and business context

## Author

**Dinesh R Poddaturi, Ph.D.**  
Economist | Pricing Strategy & Revenue Optimization  
[LinkedIn](https://www.linkedin.com/in/dinesh-r-poddaturi)

## License

MIT License
```
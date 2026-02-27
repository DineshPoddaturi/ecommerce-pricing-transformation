# Week 2 Data Validation Summary

**Date:** Friday, February 27, 2026  
**Author:** Dinesh R Poddaturi, Ph.D.  
**Purpose:** Validate data feasibility for Week 2 elasticity and choice modeling

---

## Executive Summary

This document summarizes the data validation conducted on Friday, February 27, 2026, to assess the feasibility of planned Week 2 analyses. We validated three critical aspects: (1) nested logit model viability, (2) elasticity estimation data quality, and (3) BLP estimation feasibility with seller price instruments.

**Key Findings:**
- **Nested logit is viable** - 8 of 10 buckets meet sample size and variation requirements
- **Bucket and category elasticity estimation is viable** - sufficient aggregated data
- **BLP estimation is not viable** - only 3.7% multi-seller products (insufficient for IV strategy)
- **Strategic pivot confirmed** - focus on nested logit + reduced-form elasticities

**Impact on Week 2 Plan:**
- Proceed with bucket-level log-log elasticities
- Proceed with nested logit model (bucket → product choice)
- Add category-level elasticities within top buckets
- Skip BLP estimation due to weak instrument limitations

---

## 1. Nested Logit Feasibility Assessment

**Objective:** Validate that bucket structure supports nested logit estimation

### 1.1 Sample Size Requirements

**Criterion:** Minimum 1,000 transactions per bucket for reliable estimation

**Results:**

| Bucket | Transactions | Status |
|--------|--------------|--------|
| HOME_ESSENTIALS | 33,827 | VIABLE |
| LEISURE_LIFESTYLE | 26,453 | VIABLE |
| ELECTRONICS_TECH | 17,262 | VIABLE |
| PERSONAL_CARE | 16,193 | VIABLE |
| AUTO_TOOLS | 6,682 | VIABLE |
| OFFICE_STATIONERY | 4,208 | VIABLE |
| FASHION_APPAREL | 3,734 | VIABLE |
| FOOD_BEVERAGE | 1,167 | VIABLE |
| SMALL_APPLIANCES | 993 | TOO SMALL |
| MISC | 504 | TOO SMALL |

**Conclusion:** 8 of 10 buckets (80%) meet sample size requirements. Sufficient for nested logit estimation.

---

### 1.2 Price Variation Within Buckets

**Criterion:** Coefficient of variation (CV) > 0.1 for elasticity identification

**Results:**

| Bucket | Mean Price (R$) | Std Dev (R$) | CV | Status |
|--------|-----------------|--------------|-----|--------|
| HOME_ESSENTIALS | 97.60 | 125.54 | 1.29 | EXCELLENT |
| LEISURE_LIFESTYLE | 144.83 | 206.48 | 1.43 | EXCELLENT |
| ELECTRONICS_TECH | 110.13 | 212.53 | 1.93 | EXCELLENT |
| PERSONAL_CARE | 127.90 | 173.71 | 1.36 | EXCELLENT |
| AUTO_TOOLS | 148.81 | 230.40 | 1.55 | EXCELLENT |
| OFFICE_STATIONERY | 119.99 | 94.71 | 0.79 | GOOD |
| FASHION_APPAREL | 91.76 | 98.42 | 1.07 | EXCELLENT |
| FOOD_BEVERAGE | 57.41 | 58.03 | 1.01 | EXCELLENT |
| SMALL_APPLIANCES | 353.89 | 513.38 | 1.45 | EXCELLENT |
| MISC | 82.01 | 109.87 | 1.34 | EXCELLENT |

**Conclusion:** All 10 buckets (100%) have CV > 0.1, with most showing CV > 0.7. Excellent price variation for elasticity estimation.

---

### 1.3 Product Diversity Within Buckets

**Criterion:** Minimum 50 unique products per bucket for meaningful choice sets

**Results:**

| Bucket | Unique Products | Status |
|--------|-----------------|--------|
| HOME_ESSENTIALS | 9,914 | HIGHLY DIVERSE |
| LEISURE_LIFESTYLE | 7,945 | HIGHLY DIVERSE |
| ELECTRONICS_TECH | 3,848 | HIGHLY DIVERSE |
| PERSONAL_CARE | 4,243 | HIGHLY DIVERSE |
| AUTO_TOOLS | 2,831 | HIGHLY DIVERSE |
| OFFICE_STATIONERY | 1,158 | HIGHLY DIVERSE |
| FASHION_APPAREL | 1,570 | HIGHLY DIVERSE |
| FOOD_BEVERAGE | 267 | DIVERSE |
| SMALL_APPLIANCES | 352 | DIVERSE |
| MISC | 200 | DIVERSE |

**Conclusion:** All buckets exceed 50 unique products. Product diversity supports nested logit choice set construction.

---

### 1.4 Cross-Bucket Shopping Patterns

**Objective:** Assess whether customers substitute across buckets (needed for cross-bucket elasticities)

**Results:**
- Total repeat customers: 2,989
- Shopping 1 bucket only: 1,801 (60.3%)
- Shopping 2+ buckets: 1,188 (39.7%)
- Average buckets per repeat customer: 1.42

**Interpretation:**
- 39.7% of repeat customers exhibit cross-bucket shopping
- This indicates meaningful substitution potential across buckets
- Nested logit can identify cross-bucket substitution patterns

**Conclusion:** Sufficient cross-bucket shopping behavior validates nested logit framework for substitution analysis.

---

### 1.5 Nested Logit Feasibility Summary

**Overall Assessment:** PROCEED WITH NESTED LOGIT

**Viable Buckets for Estimation (8):**
1. LEISURE_LIFESTYLE (highest priority - 29% revenue)
2. HOME_ESSENTIALS (volume leader - 31% orders)
3. PERSONAL_CARE (brand loyalty test)
4. ELECTRONICS_TECH (quality-driven)
5. AUTO_TOOLS
6. OFFICE_STATIONERY
7. FASHION_APPAREL
8. FOOD_BEVERAGE

**Exclude from Nested Logit:**
- SMALL_APPLIANCES (insufficient sample)
- MISC (insufficient sample)

---

## 2. Elasticity Data Quality Assessment

**Objective:** Validate data structure for price elasticity estimation

### 2.1 Quantity Distribution Analysis

**Dataset:** product_price_quantity (product-price combinations)

**Summary Statistics:**
- Total product-price combinations: 42,408
- Mean quantity: 2.66
- Median quantity: 1.00
- Standard deviation: 6.42
- Maximum: 404
- Products with quantity ≥ 10: 1,643 (3.9%)

**Distribution Characteristics:**
- Highly right-skewed (median = 1, mean = 2.66)
- Typical for e-commerce: most product-price combos sell 1-2 units
- Log transformation yields approximately normal distribution (suitable for log-log regression)

**Conclusion:** Quantity distribution appropriate for log-log elasticity estimation after log transformation.

---

### 2.2 Price Variation by Bucket

**Results:**

| Bucket | Products | Avg Quantity | Price Range (R$) |
|--------|----------|--------------|------------------|
| LEISURE_LIFESTYLE | 7,945 | 2.5 | 2.90 - 6,499.00 |
| HOME_ESSENTIALS | 9,914 | 2.8 | 3.06 - 6,735.00 |
| PERSONAL_CARE | 4,243 | 2.8 | 1.20 - 3,899.00 |
| ELECTRONICS_TECH | 3,848 | 3.2 | 3.90 - 6,729.00 |
| AUTO_TOOLS | 2,831 | 2.0 | 0.85 - 3,099.90 |

**Observations:**
- Wide price ranges within all buckets
- Average quantities 2-3 units (sufficient for aggregation)
- Thousands of products per bucket (robust estimates)

**Conclusion:** Sufficient price variation and sample size for bucket-level elasticity estimation.

---

### 2.3 Product-Level Price Variation

**Critical Finding:**
- Products with multiple price points: 5,900 (17.9%)
- Products with single price point: 27,051 (82.1%)

**Implication:**
- **82% of products have NO price variation** → cannot estimate individual product elasticities
- Only 17.9% products viable for product-level analysis
- This is expected in e-commerce (most products sold by single seller at fixed price)

**Examples of Products with Price Variation:**
- PERSONAL_CARE product: 6 prices, 40 units sold, range 64.00 - 139.90
- LEISURE_LIFESTYLE product: 7 prices, 15 units sold, range 54.90 - 65.90
- HOME_ESSENTIALS product: 6 prices, 16 units sold, range 89.99 - 126.98

**Strategic Decision:**
Skip individual product-level elasticity (not actionable, too sparse)
Focus on bucket-level and category-level elasticities (aggregation solves sparsity)

**Rationale:**
1. **Actionability:** Businesses make pricing decisions at bucket/category level, not individual SKUs
2. **Data availability:** Aggregation creates sufficient price variation
3. **Generalizability:** Category elasticities apply to portfolio, not just specific products
4. **Statistical power:** Aggregated data yields more reliable estimates

---

### 2.4 Elasticity Estimation Strategy

**Based on data quality assessment:**

**TIER 1: Bucket-Level Elasticities** (Primary - Strategic)
- Method: Log-log regression on `bucket_demand_monthly`
- Sample: 10 buckets × 20 months = 200 observations
- Specification: `log(bucket_quantity) = β * log(bucket_avg_price) + time_FE + controls`
- Output: One elasticity coefficient per bucket

**TIER 2: Category-Level Elasticities** (Secondary - Tactical)
- Method: Log-log regression on category-month aggregates
- Focus: Categories within top 3 buckets (LEISURE, HOME, PERSONAL_CARE)
- Sample: ~20-30 categories × 20 months = 400-600 observations
- Specification: `log(category_quantity) = β * log(category_avg_price) + time_FE + bucket_FE + controls`
- Output: One elasticity coefficient per category

**SKIP: Individual Product Elasticities**
- Reason: 82% lack price variation
- Not actionable for business strategy
- Insufficient statistical power

---

## 3. BLP Estimation Feasibility Assessment

**Objective:** Evaluate feasibility of Berry-Levinsohn-Pakes (BLP) estimation using cross-seller price instruments

### 3.1 Multi-Seller Product Analysis

**Instrument Strategy:** Use cross-seller prices as IV for own price
- **Rationale:** Seller A's price correlated with Seller B's price (competition), but Seller B's price not directly in Seller A's demand (exogeneity)
- **Standard approach:** Hausman (1996)-style instrument

**Critical Check:** How many products have multiple sellers?

**Results:**
- Total products: 32,951
- Products sold by multiple sellers: 1,225 (3.7%)
- Products sold by single seller: 31,726 (96.3%)

**Seller Distribution:**
- 1 seller: 31,726 products (96.3%)
- 2 sellers: 1,030 products
- 3 sellers: 147 products
- 4 sellers: 32 products
- 5+ sellers: 16 products

---

### 3.2 Price Variation Across Sellers

**Sample of Multi-Seller Products:**
- Product with 2 sellers: prices 87.87 - 89.77, CV = 0.02
- Product with 2 sellers: prices 39.90 - 49.99, CV = 0.16
- Product with 3 sellers: prices 53.85 - 73.29, CV = 0.16
- Product with 3 sellers: prices 71.15 - 73.90, CV = 0.02
- Product with 2 sellers: prices 17.00 - 19.00, CV = 0.08

**Observations:**
- Most multi-seller products have 2 sellers only
- Price variation across sellers is modest (CV 0.02 - 0.16)
- Limited seller competition intensity

---

### 3.3 BLP Feasibility Assessment

**Criterion for Strong Instruments:**
- Minimum 20% multi-seller products for robust IV strategy
- Sufficient first-stage F-statistic (> 10)

**Actual Data:**
- Only 3.7% multi-seller products
- 96.3% products have NO cross-seller price variation

**Conclusion:**  BLP ESTIMATION NOT VIABLE

**Reasoning:**
1. **Weak instrument problem:** Only 3.7% coverage for cross-seller price IV
2. **Weak first stage:** Insufficient variation in instruments → low F-statistic
3. **Biased estimates:** Weak IV leads to biased coefficients, large standard errors
4. **Not worth implementation cost:** 5+ days of work for unreliable estimates

---

### 3.4 Alternative IV Strategies Explored

**Option 1: Product Characteristics as Cost Shifters**
- Variables: product_weight_g, freight_value, product_volume_cm3
- Problem: May be correlated with unobserved quality (ξ)
- Validity concern: Heavier products may signal quality, not just cost

**Option 2: Seller Fixed Effects**
- Problem: Limited within-seller price variation over time
- Most sellers have stable pricing strategies

**Option 3: Geographic Instruments**
- Variables: seller_state, customer_state distance
- Problem: Distance affects demand directly (shipping costs), violates exclusion restriction

**Conclusion:** No viable alternative IV strategy identified for BLP.

---

### 3.5 Strategic Pivot Decision

**Decision:** SKIP BLP ESTIMATION

**Instead, focus on:**
1. Nested logit (no endogeneity assumption needed for substitution patterns)
2. Reduced-form log-log elasticities (interpret as conditional correlations)
3. Robustness checks with product/time fixed effects to control for unobserved heterogeneity

**Rationale:**
- Nested logit provides reliable substitution patterns without IV requirements
- Fixed effects in log-log regressions control for much of the endogeneity concern
- Transparent about limitations rather than forcing inappropriate methods

**How to Present in Portfolio:**
> "We initially considered BLP estimation to address potential price endogeneity using cross-seller prices as instruments. However, data exploration revealed only 3.7% of products are sold by multiple sellers, precluding the use of cross-seller price variation as a strong instrument. Given weak instrument concerns, we focus on nested logit and reduced-form elasticity estimation, which provide reliable estimates given the data structure."

This demonstrates:
- Knowledge of advanced methods
- Data-driven decision making
- Professional judgment (don't force methods on unsuitable data)

---

## 4. Summary of Strategic Decisions

### What We Will Estimate (Week 2)

**1. Bucket-Level Elasticities**
- Method: Log-log regression
- Sample: 8 viable buckets × 20 months
- Priority: All 8 buckets, focus on top 5
- Expected elasticity ranges:
  - LEISURE_LIFESTYLE: -1.5 to -2.5 (elastic)
  - HOME_ESSENTIALS: -0.8 to -1.2 (unit elastic)
  - PERSONAL_CARE: -0.5 to -0.8 (inelastic)

**2. Category-Level Elasticities**
- Method: Log-log regression with bucket FE
- Sample: 20-30 categories within top 3 buckets
- Focus categories:
  - LEISURE_LIFESTYLE: toys, sports_leisure, watches_gifts, pet_shop
  - PERSONAL_CARE: health_beauty, perfumery, baby
  - HOME_ESSENTIALS: furniture_decor, bed_bath_table, housewares

**3. Nested Logit Model**
- Stage 1: Bucket choice (LEISURE vs HOME vs PERSONAL_CARE vs ...)
- Stage 2: Product choice within bucket
- Output: Cross-bucket substitution elasticities, nesting parameters

**4. Propensity to Repeat Model**
- Method: Logistic regression / Random Forest
- Target: Will customer make 2nd purchase? (Yes/No)
- Features: First purchase value, basket size, bucket, review score, geography
- Output: Propensity scores, expected CLV (propensity-weighted)

---

### What We Will NOT Estimate

**Individual Product-Level Elasticities**
- Reason: 82% lack price variation; not actionable

**BLP Estimation**
- Reason: Only 3.7% multi-seller products; weak instruments

---

## 5. Week 2 Execution Plan

### Monday, March 2
**Morning (9 AM - 12 PM):**
- Create bucket-level demand dataset (bucket_demand_monthly validation)
- Estimate bucket-level elasticities (all 8 viable buckets)
- Statistical tests, confidence intervals, visualizations

**Afternoon (1 PM - 4 PM):**
- Create category-level demand dataset
- Estimate category elasticities (focus on top 3 buckets)
- Compare bucket vs category elasticities

---

### Tuesday, March 3
**Morning (9 AM - 12 PM):**
- Nested logit model specification
- Stage 1: Bucket choice estimation
- Nesting parameter estimation

**Afternoon (1 PM - 4 PM):**
- Stage 2: Within-bucket product choice
- Cross-bucket substitution matrix
- Elasticity calculations from nested logit

---

### Wednesday, March 4
**Morning (9 AM - 12 PM):**
- Propensity to repeat model
- Feature engineering, train/test split
- Model comparison (Logistic, Random Forest, XGBoost)

**Afternoon (1 PM - 4 PM):**
- Expected CLV calculation (propensity-weighted)
- Week 2 documentation
- Visualizations and summary tables

---

## 6. Expected Deliverables

### Analytical Outputs

**1. Elasticity Tables**
- Bucket-level elasticity estimates with 95% confidence intervals
- Category-level elasticity estimates (within buckets)
- Statistical significance tests

**2. Nested Logit Results**
- Cross-bucket substitution matrix (if LEISURE price ↑10%, how many switch to HOME?)
- Nesting parameters (within-bucket correlation)
- Choice probability simulations

**3. Propensity Model**
- Feature importance (what drives repeat behavior?)
- Propensity score distribution
- Expected CLV by customer segment

**4. Visualizations**
- Elasticity comparison chart (across buckets)
- Substitution heatmap (cross-bucket)
- Propensity score distribution
- Expected CLV by segment

---

### Documentation

**1. Technical Report Section**
- Methodology (log-log, nested logit, propensity model)
- Results with statistical tests
- Robustness checks
- Limitations (BLP not viable, individual product elasticity skipped)

**2. Business Interpretation**
- Strategic recommendations by bucket
- Category-specific pricing strategies
- Customer acquisition vs retention focus

---

## 7. Data Quality Summary

### Strengths
Large sample: 112,650 transactions, 95,420 customers, 32,951 products  
Rich features: Customer, seller, product characteristics, reviews  
Time series: 20 months of stable data (Jan 2017 - Aug 2018)  
Bucket structure: Well-defined, sufficient observations  
Price variation: Excellent within buckets (CV > 0.7)  
Product diversity: Thousands of products per bucket  

### Limitations
Low repeat rate: 3.12% (limits repeat purchase analysis)  
Single-seller dominance: 96.3% products have one seller  
Sparse product-level data: 82% products lack price variation  
No brand information: Cannot control for brand effects  
Platform aggregation: Cannot separate Amazon vs Mercado Livre  

### Implications for Analysis
- Focus on aggregate (bucket/category) elasticities ✓
- Use nested logit for substitution patterns ✓
- Propensity modeling instead of traditional RFM ✓
- Skip BLP and individual product elasticities ✗

---

## 8. Conclusion

Friday's data validation successfully confirmed the viability of our core Week 2 analyses while identifying necessary strategic pivots. The data strongly supports bucket-level and category-level elasticity estimation, as well as nested logit modeling for substitution patterns. The limited multi-seller product coverage (3.7%) precludes BLP estimation with cross-seller price instruments, but this does not diminish the analytical value of our planned approach.

**Key Takeaway:** Our data is well-suited for actionable, business-relevant elasticity and choice analysis. By focusing on bucket and category aggregates rather than individual products, and nested logit rather than BLP, we ensure robust, interpretable results that directly inform pricing strategy.

**Ready to Execute:** Week 2 plan is finalized and feasible. Monday morning, we begin elasticity estimation with full confidence in data quality and methodological appropriateness.

---

## Appendices

### A. Bucket Definitions

**LEISURE_LIFESTYLE:** toys, sports_leisure, watches_gifts, pet_shop, books, music, cds_dvds_musicals, art, musical_instruments, party_supplies, christmas_supplies, flowers

**HOME_ESSENTIALS:** furniture_decor, bed_bath_table, housewares, garden_tools, home_construction, construction_tools_safety, construction_tools_lights, home_comfort_2

**PERSONAL_CARE:** health_beauty, perfumery, baby, diapers_and_hygiene

**ELECTRONICS_TECH:** computers, computers_accessories, tablets_printing_image, telephony, electronics, audio, consoles_games, pc_gamer, signaling_and_security

**AUTO_TOOLS:** auto, fashion_bags_accessories, la_cuisine, air_conditioning, agro_industry_and_commerce, industry_commerce_and_business

**OFFICE_STATIONERY:** stationery, office_furniture

**FASHION_APPAREL:** fashion_female_clothing, fashion_male_clothing, fashion_underwear_beach, fashion_shoes, fashion_sport, fashion_childrens_clothes

**FOOD_BEVERAGE:** food_drink, drinks

**SMALL_APPLIANCES:** small_appliances, small_appliances_home_oven_and_coffee, fixed_telephony

**MISC:** cool_stuff, arts_and_craftmanship, cine_photo, dvds_blu_ray, home_appliances, luggage_accessories, market_place, costruction_tools_garden, security_and_services, fashio_childrens_clothes, portateis_cozinha_e_preparadores_de_alimentos, costruction_tools_construction

---

### B. Files Created Today

**Processed Data (13 files in `/data/processed/`):**
1. orders_full_clean.pkl
2. orders_full_extended.pkl - Main dataset with customer, review, seller data
3. order_items_with_bucket.pkl
4. order_items_full_clean.pkl
5. product_price_quantity.pkl - For elasticity estimation
6. product_quantity_monthly.pkl - Time series
7. bucket_demand_monthly.pkl - For bucket elasticities
8. repeat_purchases.pkl 
9. repeat_customer_orders.pkl 
10. repeat_orders_full.pkl 
11. customer_bucket_diversity.pkl
12. customer_category_products.pkl
13. same_sku_repeat.pkl

**Notebooks:**
- 01_data_exploration_business_context.ipynb (Week 1 - Complete)
- 02_segmentation_elasticity_clv.ipynb (Week 2 - In Progress)

---
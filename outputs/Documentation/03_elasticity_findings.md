# Week 2: Demand Elasticity Estimation

## Executive Summary

Attempted elasticity estimation at multiple aggregation levels to measure price sensitivity across product categories. Results show that aggregation level critically affects elasticity identification, with category-level analysis yielding 4 robust negative elasticities.

---

## 1. Bucket-Level Elasticity Estimation

### Approach
Estimated elasticities at the bucket level using monthly aggregated data (N=130 observations, 6 buckets × ~22 months).

### Results
Four specifications tested:
- **Model 1 (Pooled OLS):** β = +2.17 (p<0.001) - positive, reflecting market growth
- **Model 2 (Time FE):** β = +2.06 (p<0.001) - positive
- **Model 3 (Bucket FE):** β = +1.47 (p=0.009) - positive
- **Model 4 (Bucket + Time FE):** β = +0.09 (SE=0.19, p=0.65) - not significant [95% CI: -0.29, 0.46]

### Why Bucket-Level Failed

1. **Product mix shifts within buckets** - Customers switching between high/low price products within bucket (e.g., toys vs watches within LEISURE_LIFESTYLE) creates spurious positive correlation
2. **Aggregation bias** - Bucket-level price is weighted average, masking substitution patterns
3. **Limited variation** - Only 130 observations insufficient for identification
4. **Time trends dominate** - Market growth absorbed by fixed effects, leaving no price effect

### Methodological Lesson
Aggregation level critically affects elasticity identification. Category or product-level data provides within-bucket variation needed for reliable estimates.

---

## 2. Category-Level Elasticity Estimation

### Approach
Created category-month dataset with 1,182 observations across 62 categories. Focused on top 5 buckets (LEISURE_LIFESTYLE, HOME_ESSENTIALS, ELECTRONICS_TECH, AUTO_TOOLS, FASHION_APPAREL) yielding 943 observations across 50 categories.

### Specifications Tested

#### Specification 1: Simple Model
```
log(Quantity) = β * log(Price)
```

**Results:**
- 15 of 20 categories estimated (75% sample coverage)
- **10 negative elasticities (67%)**
- **8 statistically significant (p<0.05)**
- Mean elasticity: -0.23

**Top findings:**
- sports_leisure: -4.05*** (highly elastic)
- watches_gifts: -2.98***
- garden_tools: -2.76***
- electronics: -1.55***
- consoles_games: -1.09***

#### Specification 2: Model with Controls (PREFERRED)
```
log(Quantity) = β₁*log(Price) + β₂*log(Freight) + β₃*ReviewScore
```

**Results:**
- 20 categories estimated
- **13 negative elasticities (65%)**
- **7 statistically significant (p<0.05)**
- Mean elasticity: -0.03
- **Mean R² = 0.53** (substantial model fit)

**Pooled model:**
- Price elasticity: +0.28 (p=0.003) - still positive at aggregate
- Freight elasticity: -0.16 (p=0.44, not significant)
- Review effect: +0.10 (p=0.27, not significant)

### Robust Findings (Negative & Significant in BOTH Specifications)

| Category | Simple | Controlled | R² | Interpretation |
|----------|--------|------------|-----|----------------|
| **watches_gifts** | -2.98*** | -2.98*** | 0.89 | Highly elastic - discretionary, comparison shopping |
| **garden_tools** | -2.76*** | -2.77*** | 0.72 | Very elastic - non-essential, seasonal |
| **electronics** | -1.55*** | -2.18*** | 0.62 | Elastic - tech shoppers price-conscious |
| **consoles_games** | -1.09*** | -1.35*** | 0.56 | Moderately elastic - gaming has substitutes |

**These 4 categories show:**
- Consistent negative elasticities across specifications
- Statistical significance (p<0.01 in all cases)
- High model fit (R² 0.56-0.89)
- Economically sensible magnitudes

### Notable Findings

**Elasticity strengthened with controls:**
- Electronics: -1.55 → -2.18 (42% increase in magnitude)
- Consoles: -1.09 → -1.35 (24% increase)

**Large changes indicate confounding:**
- Sports_leisure: -4.05 → +0.63 (sign flip - freight/review confounding)
- Computers: +0.79 → -0.05 (controls reversed sign)

**Persistent positive elasticities:**
- bed_bath_table: +8.26*** (quality signaling/Veblen effect)
- fashion_bags: +3.02*** (luxury positioning)
- furniture_decor: +2.06* (quality perception)

### Summary Statistics

**Agreement between models:**
- Both negative: 8 of 15 categories (53%)
- Mean R² improvement: 0.59 (controls substantially improve fit)

**Controls matter most for:**
- Sports_leisure (Δ = +4.68)
- Bed_bath_table (Δ = +1.67)
- Auto (Δ = +0.78)

---

## 3. Business Implications

### Price-Sensitive Categories (High Elasticity)
**Watches & gifts, garden tools, electronics, gaming**
- 10% price increase → 14-30% demand decrease
- Limited pricing power
- Discounts/promotions highly effective
- Focus on volume over margin

### Quality-Signal Categories (Positive Elasticity)
**Furniture, home textiles, fashion accessories**
- Higher prices may signal quality (Veblen goods)
- Premium positioning opportunity
- Price increases may not hurt demand
- Requires product-level investigation

---

## 4. Limitations & Future Work

### Current Limitations
1. **Observational data** - No controlled price variation, time trends confound estimates
2. **Aggregation** - Even category-level masks product-level substitution
3. **Endogeneity** - Prices may respond to unobserved demand shocks
4. **Sample size** - Limited monthly observations per category (15-24 months)

### Recommended Approaches
1. **Natural experiments** - Policy changes, supply shocks, or price discontinuities
2. **A/B testing** - Randomized price experiments for causal estimates
3. **Product-level analysis** - Where sufficient price variation exists
4. **Instrumental variables** - If valid instruments available (not present in current data)

---

## 5. Alternative Approach: Nested Logit

### Why Nested Logit May Work Better

**Different from log-log regression:**
- **Log-log:** Measures quantity response to price (demand elasticity)
- **Nested logit:** Measures choice probability response to price (substitution patterns)

**Advantages:**
1. **Uses individual choices** - Not aggregated quantity (more observations)
2. **Cross-bucket substitution** - Where do customers go when price increases?
3. **Avoids aggregation bias** - Operates on transaction-level data

**Structure:**
- **Stage 1:** Bucket choice (LEISURE vs HOME vs PERSONAL_CARE vs...)
- **Stage 2:** Product choice within bucket

**Output:**
- Cross-bucket substitution elasticities
- Nesting parameters (within vs between-bucket substitution)
- Complements demand elasticity estimates

### Status
Not implemented in current analysis. Potential future extension if cross-bucket substitution patterns needed for business strategy.

---

## 6. Conclusions

### Key Findings
1. **Aggregation matters** - Bucket-level fails, category-level works
2. **4 robust elasticities identified** - Watches, garden tools, electronics, gaming
3. **Controls improve estimates** - Freight and reviews matter for model fit
4. **Mixed results inform strategy** - Some categories price-sensitive, others quality-signaling

### Methodological Contributions
- Demonstrates importance of aggregation level in elasticity estimation
- Shows value of robustness checks across specifications
- Illustrates challenges of observational e-commerce data

### Honest Assessment
While we identified 4 statistically robust negative elasticities with good model fit, the presence of positive elasticities in some categories and sensitivity to specification choice suggests caution in causal interpretation. Results should be viewed as conditional correlations rather than causal effects. Controlled experiments would be needed for definitive pricing recommendations.

---

## Data & Code
- **Dataset:** Olist Brazilian E-commerce (Sept 2016 - Sept 2018)
- **Sample:** 943 category-month observations, 50 categories, 5 buckets
- **Code:** Available in `notebooks/02_segmentation_elasticity_clv.ipynb`
- **Results:** Saved in `outputs/elasticity_final_summary.csv`
# REVISED: COMPREHENSIVE 6-WEEK OLIST PRICING TRANSFORMATION PROJECT ROADMAP

## REVISION NOTES (Added Feb 26, 2026)

**Why This Revision:**
After completing Week 1 data exploration, four critical findings emerged that require strategic pivots in our analytical approach:

1. **Low Repeat Rate (3.12%)**: Traditional RFM analysis won't work - this is an acquisition marketplace, not retention platform
2. **Strategic Bucketing Complete**: 10 well-defined buckets enable nested logit modeling (bucket choice → product choice)
3. **Product Mix Shift Discovery**: Repeat customers explore portfolios (primary → complementary), not bargain hunting
4. **Value-Based Pricing**: Price decoupled from physical attributes (0.34 correlation) - focus on willingness-to-pay

**Key Strategic Shifts:**
- Replace RFM with first-purchase segmentation + propensity modeling
- Add nested logit model (leverages bucket structure)
- Focus on cross-sell optimization (basket building) over repeat purchase
- Prioritize LEISURE_LIFESTYLE bucket (29% revenue, lowest weight correlation)
- Optimize acquisition and sequential journeys, not loyalty programs

---

## PROJECT OVERVIEW

**Title:** E-Commerce Marketplace Pricing Transformation: A Strategic Approach to Revenue & Customer Lifetime Value Optimization

**Business Context:**  
A Brazilian e-commerce marketplace (Olist) facing:
- High price variance for identical products (seller competition)
- Very low customer repeat purchase rates (3.12%)
- Margin pressure from aggressive pricing
- Suboptimal platform commission structure

**Objective:**  
Develop comprehensive pricing strategy combining customer segmentation, marketplace dynamics analysis, and CLV optimization to drive sustainable revenue growth through acquisition excellence and same-order cross-sell optimization.

---

## WEEK 1: DATA FOUNDATION & BUSINESS CONTEXT COMPLETED

### 1. Understanding the Tables

**Core Transaction Data:**
- `olist_orders_dataset.csv` → Order-level activity, time series (order_id, customer_id, order_status, timestamps)
- `olist_order_items_dataset.csv` → Transaction-level pricing analysis (order_id, product_id, seller_id, price, freight_value)
- `olist_products_dataset.csv` → Product characteristics for segmentation (product_id, category, dimensions, photos)

**Entity Data:**
- `olist_sellers_dataset.csv` → Seller-level analysis (seller_id, location)
- `olist_customers_dataset.csv` → Customer segmentation, geolocation (customer_id, location)

**Enrichment Data:**
- `olist_order_reviews_dataset.csv` → Customer satisfaction, quality-adjusted pricing (review_id, order_id, review_score)
- `olist_geolocation_dataset.csv` → Geographic segmentation, distance-based logistics
- `olist_payments_dataset.csv` → Payment behavior insights
- `olist_category_name_translation.csv` → English category names

### 2. Data Cleaning & Merging 

**Completed Tasks:**
- Joined tables: orders → order_items → products → customers → sellers → reviews
- Converted timestamps to datetime objects
- Handled missing values (freight_value, review_score, product attributes)
- Removed duplicates and outliers
- Created master analytical dataset

**Feature Engineering (Completed):**
- Order-level: total_spend, num_items, avg_item_price, shipping_cost_ratio
- Time features: day_of_week, month, year_month
- Geographic: customer_state, seller_state
- **Strategic Buckets: 10 product buckets based on purchase behavior**

### 3. Business Problem Framing 

**Key Findings:**
- **Repeat Rate: 3.12%** (96.9% one-time buyers) - Acquisition marketplace
- **Price dispersion:** Varies significantly across sellers for identical products
- **Repeat "discount" myth:** -15% is product mix shift, not bargain hunting
- **Same-SKU repurchases:** 90.3% paid identical prices (no deal-seeking)
- **Product exploration:** 67% of repeat customers buy different products in same category
- **Stable pricing environment:** Minimal seasonal variation (good for elasticity analysis)
- **Value-based pricing:** Price weakly correlated with weight (0.34) and volume (0.30)

**Strategic Bucket Performance:**
| Bucket | Orders | Revenue (BRL) | Avg Price | % Orders | % Revenue |
|--------|---------|---------------|-----------|----------|-----------|
| **LEISURE_LIFESTYLE** | 26,453 | 3,831,261 | 145 | 24% | **29%** |
| **HOME_ESSENTIALS** | 33,827 | 3,301,515 | 98 | **31%** | 25% |
| **PERSONAL_CARE** | 16,193 | 2,071,139 | 128 | 15% | 16% |
| **ELECTRONICS_TECH** | 17,262 | 1,901,030 | 110 | 16% | 15% |

**Deliverables Completed:**
- Jupyter notebook: `01_data_exploration_business_context.ipynb`
- Documentation: `00_data_overview.md` and `01_data_exploration.md` (6,000+ words)
- GitHub repository updated with visualizations

---

## WEEK 2: CUSTOMER SEGMENTATION & PRICE ELASTICITY (REVISED)

**STRATEGIC PIVOT:** Drop traditional RFM analysis (insufficient repeat data). Focus on first-purchase optimization, propensity modeling, and nested logit framework.

### 1. Customer Segmentation (REVISED APPROACH)

**A) First-Purchase Behavioral Segmentation**

**By Transaction Value:**
- High-value first purchase (>200 BRL) - premium customers
- Mid-value (75-200 BRL) - typical customers
- Low-value (<75 BRL) - price-sensitive or exploratory

**By Bucket Preference:**
- LEISURE_LIFESTYLE buyers (discretionary, likely elastic)
- HOME_ESSENTIALS buyers (functional, moderate elasticity)
- PERSONAL_CARE buyers (brand loyalty, likely inelastic)
- ELECTRONICS buyers (quality-focused, research-driven)

**By Basket Characteristics:**
- Single-item buyers (simplicity or targeted purchase)
- Multi-item basket builders (cross-sell opportunity)
- High freight-to-price ratio (distance-sensitive)

**B) Propensity to Repeat Model (NEW - HIGH PRIORITY)**

**Objective:** Predict which first-time customers will become the rare 3% who return

**Method:**
- Binary classification: Will customer make 2nd purchase? (Yes/No)
- Algorithm: Logistic regression, Random Forest, XGBoost

**Features:**
- First purchase value
- Number of items in first order
- Bucket purchased from
- Product price tier (low/mid/high within category)
- Review score left
- Shipping cost ratio
- Geographic location (state)
- Day of week, month purchased

**Output:**
- Propensity score for each customer
- Feature importance (what drives repeat behavior?)
- Segment customers by propensity (high/medium/low)

**Why This Matters:**
- Focus acquisition spending on high-propensity profiles
- Different pricing strategies for likely-repeat vs one-time customers
- Calculate expected CLV (not just realized CLV)

**C) Geographic Segmentation**

- Urban vs rural (shipping cost impact)
- High-income states (Sao Paulo, Rio) vs low-income
- State-level price sensitivity
- Distance from distribution centers

**D) Quality-Tier Segmentation Within Buckets**

- Low-price tier (bottom 25% within bucket)
- Mid-price tier (25-75%)
- High-price tier (top 25%)
- Analyze if high-price = quality signal or just expensive

**DROPPED:** Traditional RFM analysis
**Reason:** Only 3% repeat rate - insufficient data for robust Recency/Frequency analysis. Focus on predicting who WILL repeat rather than analyzing who already has.

---

### 2. Price Elasticity Estimation (ENHANCED APPROACH)

**A) Nested Logit Model (NEW - LEVERAGES BUCKET STRUCTURE)**

**Why Nested Logit:**
- Customers make TWO decisions: (1) Which bucket? (2) Which product within bucket?
- Captures cross-bucket substitution patterns
- Aligns with actual shopping behavior

**Model Structure:**
```
Stage 1: Bucket Choice
P(bucket_j) = f(bucket_avg_price, bucket_characteristics, customer_segment)

Stage 2: Product Choice Within Bucket
P(product_i | bucket_j) = f(product_price, product_attributes, reviews, seller_rating)
```

**Elasticity Estimates:**
- **Own-bucket elasticity:** How does LEISURE_LIFESTYLE demand respond to its own price changes?
- **Cross-bucket elasticity:** If LEISURE_LIFESTYLE prices increase, do customers shift to HOME_ESSENTIALS?
- **Within-bucket elasticity:** Product-level price sensitivity within LEISURE_LIFESTYLE

**Implementation:**
- Use `statsmodels` or custom likelihood function
- Control for: product characteristics, time effects, geographic effects
- Estimate by customer segment (high-value vs price-sensitive)

**Expected Outputs:**
- Bucket-level elasticity matrix (10x10 substitution patterns)
- Product-level elasticities for high-priority buckets
- Visualization: Heatmap of cross-bucket substitution

---

**B) Bucket-Level Elasticities (PRIORITY ORDER)**

**1. LEISURE_LIFESTYLE (HIGHEST PRIORITY)**
- 29% of revenue
- Discretionary spending (expect high elasticity)
- Lowest price-weight correlation (0.31) = pure value pricing
- Categories: toys, sports, watches, gifts, books, pet supplies

**Expected elasticity: -1.5 to -2.5** (elastic)

**2. HOME_ESSENTIALS (VOLUME LEADER)**
- 31% of orders
- Functional purchases (expect moderate elasticity)
- Moderate price-weight correlation (0.48)
- Categories: furniture, bed/bath, housewares, garden

**Expected elasticity: -0.8 to -1.2** (unit elastic)

**3. PERSONAL_CARE (BRAND LOYALTY TEST)**
- 16% of revenue
- Brand preferences likely (expect lower elasticity)
- Moderate price-weight correlation (0.43)
- Categories: health_beauty, perfumery, baby products

**Expected elasticity: -0.5 to -0.8** (inelastic)

**4. ELECTRONICS_TECH (QUALITY-DRIVEN)**
- 15% of revenue
- Research-driven purchases (expect moderate elasticity)
- Value-based pricing (0.37 correlation)

**Expected elasticity: -0.8 to -1.3** (moderate)

**Method:**
```python
# Log-log regression
log(quantity) = β0 + β1*log(price) + bucket_FE + time_FE + controls

Where β1 = own-price elasticity
```

**Controls:**
- Product fixed effects (quality/brand)
- Time fixed effects (seasonality)
- Seller rating
- Review scores
- Freight costs
- Geographic market

---

**C) Product-Level Elasticities (SELECTIVE)**

**Focus on:**
- Products with sufficient price variation (CV > 0.3)
- High-volume products (>50 transactions)
- Within LEISURE_LIFESTYLE and HOME_ESSENTIALS buckets only

**Avoid:**
- Products with zero price variation (MAP policies, single seller)
- Low-volume products (unreliable estimates)

---

**D) Sequential Purchase Elasticity (NEW - KEY INSIGHT)**

**Discovery from Week 1:** Repeat customers exhibit primary → complementary purchase patterns

**Analysis:**
- How does price of primary product affect probability of buying complementary product?
- Example patterns:
  - Expensive bed frame → cheaper bedding
  - Premium skincare set → basic refills
  - Watch → accessories

**Method:**
```python
# Conditional purchase probability
P(buy_complementary | bought_primary) = f(primary_price, time_between_purchases, customer_segment)

# Estimate using:
- Logistic regression
- Survival analysis (time-to-second-purchase)
```

**Why This Matters:**
- Optimize primary product pricing (acquire customer)
- Extract margin from complementary products (monetize relationship)
- "Loss leader" strategies (cheap primary, expensive accessories)

**Expected Output:**
- Complementary product pairs by bucket
- Optimal pricing strategy: primary vs complementary
- Revenue impact of sequential pricing optimization

---

### 3. Customer Lifetime Value (REVISED - PROPENSITY-WEIGHTED)

**Challenge:** Only 3% repeat, so traditional CLV = misleading

**Revised Approach: Expected CLV**

**Formula:**
```
Expected CLV = First_Purchase_Value + (P(repeat) × Expected_Repeat_Value × Expected_Frequency) - CAC

Where:
- P(repeat) = from propensity model (Section 1B)
- Expected_Repeat_Value = observed avg for the 3% who do repeat
- Expected_Frequency = observed avg repeat frequency (1.05 for repeat customers)
- CAC = customer acquisition cost estimate
```

**Segment-Level CLV:**

**High-Propensity Segment (predicted to repeat):**
- First purchase: 150 BRL (avg)
- P(repeat): 15-20% (modeled)
- Repeat value: 100 BRL (complementary products)
- Expected frequency: 1.2
- **Expected CLV: 150 + (0.18 × 100 × 1.2) = 171.6 BRL**

**Low-Propensity Segment (one-time buyers):**
- First purchase: 80 BRL (avg)
- P(repeat): 1-2%
- **Expected CLV ≈ 80 BRL** (essentially first purchase only)

**Strategic Implications:**
- High-propensity customers worth up to 2x acquisition cost
- Low-propensity: optimize first-purchase value (no future expected)
- Focus retention efforts ONLY on high-propensity segment
- Fashion/Food categories: higher repeat rates (7.2%, 6.1%) justify loyalty programs

**Alternative: First-Purchase Value Optimization**

Since 96.9% are one-time buyers, focus on maximizing initial transaction:
- Cross-sell in SAME order (basket size optimization)
- Bundle recommendations at checkout
- "Complete the set" suggestions
- Volume discounts for multi-item purchases

**Metric Shift:**
- **Don't focus on:** Repeat purchase rate improvement (unrealistic given market dynamics)
- **DO focus on:** Average order value (AOV) increase through cross-sell

---

### Deliverables - Week 2

**Notebook:** `02_segmentation_elasticity_clv_REVISED.ipynb`

**Sections:**

1. **Customer Segmentation**
   - First-purchase behavioral segments
   - Propensity to repeat model (with feature importance)
   - Geographic patterns
   - Quality-tier analysis
   - **Documentation note:** Why RFM was not used (data limitations)

2. **Nested Logit Model**
   - Bucket-level choice estimation
   - Product-level choice within buckets
   - Cross-bucket substitution matrix
   - Interpretation for pricing strategy

3. **Price Elasticity Analysis**
   - Bucket-level elasticities (all 10 buckets)
   - Product-level elasticities (LEISURE_LIFESTYLE, HOME_ESSENTIALS)
   - Sequential purchase patterns (primary → complementary)
   - Cross-sell elasticities

4. **Expected CLV Calculation**
   - Propensity-weighted CLV by segment
   - Segment profiles and characteristics
   - Customer acquisition cost (CAC) thresholds
   - First-purchase value optimization framework

**Visualizations:**
- Customer segment profiles (bar charts, distributions)
- Propensity model feature importance
- Nested logit choice tree diagram
- Elasticity curves by bucket
- Cross-bucket substitution heatmap
- Sequential purchase flow diagram
- Expected CLV by propensity segment

---

## WEEK 3: SELLER ANALYSIS & MARKETPLACE DYNAMICS (REVISED)

**STRATEGIC ADDITIONS:** Sequential purchase journey mapping, basket optimization, category portfolio strategy

### 1. Seller Performance Analysis

**Metrics:**
- High volume sellers (top 20%)
- High ratings (>4.5 stars avg)
- High cancellation/return rates
- Delivery performance
- Price positioning (premium vs discount)

**Seller Strategies:**
- Identify pricing patterns
- Volume vs margin trade-offs
- Quality positioning

---

### 2. Marketplace Dynamics (TWO-SIDED MARKET)

**Price Dispersion Analysis:**
- Same product, different sellers → measure variance
- Are customers paying premium for high-rated sellers?
- Race-to-bottom dynamics in competitive categories?

**Platform Commission Impact:**
- Current take rate estimation
- Simulate commission changes:
  - 5% commission → impact on seller pricing & GMV
  - 10% commission → baseline scenario
  - 15% commission → margin optimization
- Show GMV vs platform revenue trade-offs

**Buyer-Seller Matching:**
- Do high-rated sellers charge premium?
- Customer willingness to pay for quality (reviews impact on price)
- Platform curation strategies

---

### 3. Sequential Purchase Journey Mapping (NEW - BASED ON WEEK 1 FINDING)

**Discovery:** Repeat customers (the 3%) exhibit systematic primary → complementary patterns

**Analysis:**

**A) Identify Primary-Complementary Pairs by Bucket:**

**HOME_ESSENTIALS:**
- Primary: Bed frame, dining table, sofa
- Complementary: Bedding, tableware, cushions, decor
- Pattern: Expensive durable → cheaper accessories

**PERSONAL_CARE:**
- Primary: Premium skincare sets, anti-aging products
- Complementary: Basic shampoo, refills, cotton pads
- Pattern: Discovery purchase → routine replenishment

**LEISURE_LIFESTYLE:**
- Primary: Expensive watch, sports equipment
- Complementary: Watch bands, accessories, pet toys
- Pattern: Featured item → add-ons

**B) Sequential Timing Analysis:**
- How long between primary and complementary purchase?
- Does time lag vary by category?
- Optimal timing for retargeting campaigns

**C) Price Differential Analysis:**
- What's typical price ratio? (primary:complementary = 5:1? 3:1?)
- Revenue breakdown (% from primary vs complementary)

**D) Conversion Probability:**
- P(buy complementary | bought primary) by category
- Which primary products best predict complementary purchases?

**Strategic Implications:**
- **Competitive pricing on primary products** (customer acquisition)
- **Margin extraction from complementary products** (monetization)
- **Cross-sell campaigns** targeting primary product buyers
- **Bundle offerings** at point of primary purchase

---

### 4. Basket Optimization (NEW - MORE IMPORTANT THAN REPEAT)

**Insight:** 96.9% buy only once → optimize SAME-ORDER value, not future orders

**Analysis:**

**A) Multi-Item Order Patterns:**
- % of orders with 2+ items (by bucket)
- Average items per order
- Most common product combinations

**B) Cross-Category Baskets:**
- Which buckets are purchased together?
- HOME_ESSENTIALS + PERSONAL_CARE?
- LEISURE_LIFESTYLE + FASHION_APPAREL?

**C) Price Elasticity of Basket Size:**
- Does lower price on first item increase probability of adding second item?
- Volume discount effectiveness

**D) Bundle Recommendations:**
- Data-driven bundles based on co-purchase patterns
- "Complete the room" for HOME_ESSENTIALS
- "Starter kit" for PERSONAL_CARE
- "Gift set" for LEISURE_LIFESTYLE

**E) Loss Leader Strategy:**
- Identify high-traffic, low-margin products that drive basket additions
- Example: Cheap organizational bins → expensive furniture
- Optimize portfolio: some products acquire, others monetize

**Strategic Implications:**
- Increase Average Order Value (AOV) by 15-20%
- Margin improvement through mix (low-margin acquisition + high-margin add-ons)
- Checkout optimization (recommend complementary items)

---

### 5. Category Portfolio Strategy (ENHANCED)

**LEISURE_LIFESTYLE Deep-Dive (Highest Priority):**
- 29% of revenue, 24% of orders
- Lowest price-weight correlation (0.31) = pure value-based pricing
- Discretionary spending = likely elastic
- **Recommendation:** Aggressive pricing optimization, bundle strategies

**Fashion/Food Retention Focus (Only Categories with Repeat):**
- Fashion: 7.2% repeat rate (2x overall average)
- Food: 6.1% repeat rate
- **Recommendation:** These are the ONLY categories where loyalty programs make sense
- Subscription models for food/beverage
- Seasonal reminders for fashion

**One-Time Purchase Categories (Electronics, Auto, Small Appliances):**
- 3.4-3.6% repeat rates (at or below average)
- High-value, infrequent replacement cycles
- **Recommendation:** Pure acquisition pricing, competitive benchmarking, no retention investment

**HOME_ESSENTIALS Sequential Strategy:**
- 31% of orders (volume leader)
- 5.3% repeat rate (above average for durables)
- Sequential purchase pattern confirmed
- **Recommendation:** Optimize primary-to-complementary journey, "complete the room" bundles

---

### 6. Geographic Analysis

**Logistics & Pricing:**
- High-volume zip codes/states
- Shipping cost sensitivity by region
- Distance-based pricing adjustments
- Urban vs rural patterns

---

### Deliverables - Week 3

**Notebook:** `03_seller_marketplace_dynamics_REVISED.ipynb`

**Sections:**

1. **Seller Performance Analysis**
   - Seller segmentation and strategies
   - Quality-price relationship
   - Delivery performance impact

2. **Marketplace Dynamics**
   - Price dispersion analysis
   - Platform commission optimization
   - Buyer-seller matching patterns

3. **Sequential Purchase Journey Mapping** (NEW)
   - Primary-complementary product pairs
   - Timing analysis
   - Conversion probabilities
   - Pricing strategy implications

4. **Basket Optimization** (NEW)
   - Multi-item order patterns
   - Cross-category basket analysis
   - Bundle recommendations
   - Loss leader identification

5. **Category Portfolio Strategy**
   - LEISURE_LIFESTYLE deep-dive
   - Fashion/Food retention focus
   - One-time category strategies
   - HOME_ESSENTIALS sequential optimization

6. **Geographic Analysis**
   - Regional pricing patterns
   - Logistics cost impact
   - State-level opportunities

**Visualizations:**
- Seller performance dashboard
- Price dispersion box plots
- Platform commission scenario analysis
- Sequential purchase flow diagrams
- Basket composition charts
- Category portfolio matrix
- Geographic heatmaps

---

## WEEK 4: OPTIMIZATION & CLV-DRIVEN STRATEGY (REVISED)

**STRATEGIC SHIFT:** Focus on acquisition excellence and same-order optimization, not repeat purchase optimization

### 1. Pricing Optimization Framework (REVISED)

**Three Objective Functions:**

**A. First-Purchase Value Maximization (REVISED FROM "REVENUE MAX"):**
```
Maximize: Average Order Value (AOV) = Price × Quantity + Cross-Sell Value
Subject to: 
- Demand elasticity constraints by bucket
- Competitive positioning requirements
- Acquisition cost coverage
```

**Focus:** Since 96.9% buy only once, optimize initial transaction value

**B. Margin Maximization (Portfolio Approach):**
```
Maximize: (Revenue - Costs) across product portfolio
Subject to: 
- Minimum volume requirements (traffic drivers)
- Basket composition constraints (loss leaders + high-margin)
```

**Focus:** Optimize mix of acquisition products (low margin) + monetization products (high margin)

**C. Expected CLV Maximization (Propensity-Weighted):**
```
Maximize: Expected CLV = First_Purchase + [P(repeat) × Repeat_Value]
Subject to: 
- Customer acquisition cost constraints
- Propensity model accuracy
- Retention program ROI
```

**Focus:** Different strategies for high-propensity (invest in retention) vs low-propensity (maximize first purchase)

**Show Trade-offs:**
- AOV max → may reduce conversion rate
- Margin max → may sacrifice volume
- Expected CLV max → balanced, data-driven approach

---

### 2. Segmented Pricing Strategy (ACQUISITION-FOCUSED)

**By Customer Propensity Segment:**

**High-Propensity Customers (predicted to repeat):**
- Competitive pricing on first purchase (acquire customer)
- Cross-sell complementary products in same order
- Follow-up campaigns for sequential purchases
- Loyalty rewards after 2nd purchase

**Low-Propensity Customers (one-time buyers):**
- Maximize first-purchase value (no future expected)
- Aggressive cross-sell at checkout
- Bundle offers
- No retention investment

**By Product Category/Bucket:**

**LEISURE_LIFESTYLE:**
- Dynamic pricing based on demand elasticity
- Bundle optimization (toy + gift wrap, watch + accessories)
- Promotional pricing for traffic generation
- Premium pricing for exclusive/branded items

**HOME_ESSENTIALS:**
- Competitive pricing on primary products (bed, furniture)
- Sequential journey optimization (bed → bedding → decor)
- "Complete the room" bundles
- Volume discounts for multi-item purchases

**PERSONAL_CARE:**
- Starter kit pricing (premium discovery sets)
- Refill subscriptions (only for high-propensity customers)
- Volume discounts on basics
- Premium positioning for specialized products

**ELECTRONICS/TECH:**
- Competitive acquisition pricing (no repeat expected)
- Quality signaling (don't discount too heavily)
- Accessory bundling (case, charger with phone)
- Price matching strategies

**FASHION/FOOD (Only Retention-Focused Categories):**
- Seasonal promotions (fashion)
- Subscribe & save (food/beverage)
- Loyalty rewards
- Personalized recommendations

---

### 3. Scenario Simulations (Board-Level Metrics)

**Scenario 1: Cross-Sell Optimization in LEISURE_LIFESTYLE**
- **Action:** Implement "complete the gift" bundles (toy + gift wrap + card)
- **Mechanism:** Increase items per order from 1.0 → 1.3
- **Impact:**
  - AOV increase: +18% (145 BRL → 171 BRL)
  - Basket size increase: +30%
  - Margin improvement: +4% (accessories higher margin)
  - Expected GMV impact: +$680K annually in LEISURE_LIFESTYLE

**Scenario 2: Sequential Journey Optimization in HOME_ESSENTIALS**
- **Action:** Aggressive primary product pricing + targeted complementary offers
- **Mechanism:** Reduce bed frame price 10% → increase complementary purchase probability 15%
- **Impact:**
  - Primary product revenue: -10% (bed frames)
  - Complementary product revenue: +25% (bedding, decor)
  - Net revenue: +8%
  - Expected GMV impact: +$264K annually

**Scenario 3: Loss Leader Strategy**
- **Action:** Identify high-traffic products, reduce margin to drive basket additions
- **Mechanism:** 20% margin reduction on 5 high-volume products → 15% increase in basket size
- **Impact:**
  - Direct revenue on loss leaders: -$150K
  - Incremental revenue from basket additions: +$420K
  - Net impact: +$270K (+1.8% GMV)

**Scenario 4: Propensity-Based Targeting**
- **Action:** Invest in retention ONLY for high-propensity segment (top 20%)
- **Mechanism:** Email campaigns, personalized offers to predicted repeaters
- **Cost:** $50K retention program investment
- **Impact:**
  - Conversion of high-propensity to actual repeat: 15% → 25%
  - Incremental repeat purchases: +1,200 orders/year
  - Incremental revenue: +$144K
  - ROI: 2.9x

**Scenario 5: Fashion/Food Subscription Model**
- **Action:** Offer subscriptions for food/beverage, seasonal fashion boxes
- **Mechanism:** Convert 10% of Fashion/Food customers to subscription (leverage 7.2% and 6.1% repeat rates)
- **Impact:**
  - Subscription revenue: +$95K annually
  - Retention improvement: Fashion repeat 7.2% → 12%, Food 6.1% → 10%
  - Customer lifetime value increase: +45% in these categories

**Scenario 6: Dynamic Pricing in LEISURE_LIFESTYLE**
- **Action:** Implement real-time pricing adjustments based on demand signals
- **Mechanism:** Adjust prices ±10% based on: time of day, inventory levels, competitor pricing
- **Impact:**
  - Revenue increase: +7% (capture willingness-to-pay variation)
  - Margin improvement: +2.5%
  - Expected GMV impact: +$268K

**Consolidated Impact Summary:**

| Scenario | GMV Impact | Margin Impact | Complexity | Priority |
|----------|-----------|---------------|------------|----------|
| Cross-sell optimization | +$680K (+5.1%) | +4% | Medium | **HIGH** |
| Sequential journey | +$264K (+2.0%) | +3% | Low | **HIGH** |
| Loss leader strategy | +$270K (+1.8%) | -1% (short-term) | Medium | MEDIUM |
| Propensity targeting | +$144K (+1.1%) | Neutral | High | MEDIUM |
| Fashion/Food subscription | +$95K (+0.7%) | +2% | High | LOW |
| Dynamic pricing | +$268K (+2.0%) | +2.5% | Very High | LOW |
| **TOTAL POTENTIAL** | **+$1.72M (+12.7%)** | **+5-7%** | - | - |

**Metrics to Track:**
- GMV (Gross Merchandise Value)
- Average Order Value (AOV)
- Items per order (basket size)
- Customer acquisition cost (CAC)
- Expected CLV (propensity-weighted)
- First-purchase value
- Margin % by category
- Conversion rate by segment

---

### 4. Bundle & Cross-Sell Strategies (ENHANCED)

**Data-Driven Bundles:**
- Basket analysis (products frequently bought together)
- Sequential analysis (primary → complementary patterns)
- Cross-category opportunities

**Recommended Bundles:**

**HOME_ESSENTIALS:**
- "Complete the Bedroom": Bed frame + mattress + bedding (15% bundle discount)
- "Dining Set": Table + chairs + tableware (10% discount)
- "Bathroom Refresh": Shower curtain + bath mat + towels (12% discount)

**LEISURE_LIFESTYLE:**
- "Gift Ready": Product + gift wrap + greeting card (free gift wrap with product)
- "Pet Starter Kit": Pet bed + bowls + toys (8% discount)
- "Fitness Bundle": Equipment + apparel + accessories (10% discount)

**PERSONAL_CARE:**
- "Skincare Routine": Cleanser + toner + moisturizer (20% off set vs individual)
- "Beauty Basics": Foundation + brushes + remover (15% discount)
- "Baby Essentials": Diapers + wipes + cream (subscribe & save option)

**Cross-Sell Timing:**
- **At checkout:** "Complete your purchase" recommendations
- **Post-primary-purchase:** Email campaign with complementary products (for high-propensity customers only)
- **Seasonal:** Holiday bundles, back-to-school, home refresh campaigns

---

### Deliverables - Week 4

**Notebook:** `04_optimization_clv_scenarios_REVISED.ipynb`

**Sections:**

1. **Optimization Framework**
   - AOV maximization model
   - Portfolio margin optimization
   - Expected CLV optimization
   - Trade-off analysis

2. **Segmented Pricing Strategy**
   - High-propensity vs low-propensity strategies
   - Bucket-specific pricing approaches
   - Fashion/Food retention programs

3. **Scenario Simulations**
   - 6 detailed scenarios with impact quantification
   - Consolidated impact summary
   - Risk and sensitivity analysis

4. **Bundle & Cross-Sell Strategies**
   - Data-driven bundle recommendations
   - Cross-sell timing optimization
   - Expected AOV impact

**Visualizations:**
- Optimization trade-off curves
- Scenario impact waterfall charts
- Bundle effectiveness analysis
- Segment-based strategy matrix
- Expected CLV by propensity segment
- Sensitivity analysis (tornado charts)

**Results Summary:**
- Expected GMV increase: **+12.7% (+$1.72M)**
- Margin improvement: **+5-7%**
- AOV increase: **+15-20%**
- Focus shift: Acquisition excellence + same-order optimization (NOT repeat rate improvement)

---

## WEEK 5: EXECUTIVE DECK & DASHBOARD (REVISED)

### 1. Executive Recommendation Deck (10-15 slides) - UPDATED FINDINGS

**Structure:**

**Slide 1: Executive Summary**
- Problem statement
- Key findings (updated with Week 1 discoveries)
- Expected impact: **+12-15% GMV, +15-20% AOV, +5-7% margin**

---

**Slides 2-3: Situation Analysis (REVISED)**

**Market Context:**
- Brazilian e-commerce, competitive landscape
- Multi-platform aggregation marketplace (Olist)

**Current Challenges (UPDATED):**
- **Low retention: 3.12% repeat rate** (96.9% one-time buyers)
  - This is an **acquisition marketplace**, not retention platform
  - Traditional loyalty programs won't work
- **High price variance:** Up to 40% dispersion for identical products
- **Margin pressure:** Seller competition driving race-to-bottom
- **Suboptimal monetization:** Missing cross-sell opportunities

**Critical Insight:**
> "The marketplace paradox: Customers are loyal to the PLATFORM, not individual sellers. Success requires acquisition excellence and same-order value optimization, not repeat purchase strategies."

---

**Slides 4-6: Analysis & Insights (UPDATED)**

**1. Customer Behavior Reality:**
- Only 3.12% repeat purchase rate
- 96.9% of customers buy exactly once
- **Implication:** Focus on first-purchase value, not retention

**Strategic Segmentation (REVISED):**
- **Propensity-based:** High-propensity (20%) vs low-propensity (80%)
- **Bucket preference:** LEISURE_LIFESTYLE (29% revenue), HOME_ESSENTIALS (31% orders)
- **Basket behavior:** Single-item (majority) vs multi-item basket builders

**2. The "Repeat Discount" Myth Debunked:**
- Apparent -15% discount for repeat customers
- **Reality:** Product mix shift (primary → complementary items)
- **Evidence:** 90.3% of same-SKU repurchases at identical prices (no bargain hunting)
- **Implication:** Don't offer loyalty discounts; optimize cross-sell instead

**3. Price Elasticity Findings (BY BUCKET):**
- **LEISURE_LIFESTYLE:** -1.8 elasticity (highly elastic, 29% revenue opportunity)
- **HOME_ESSENTIALS:** -1.0 elasticity (unit elastic, volume leader)
- **PERSONAL_CARE:** -0.6 elasticity (inelastic, brand loyalty exists)
- **ELECTRONICS:** -1.1 elasticity (moderate, quality-focused)

**4. Marketplace Dynamics:**
- **Nested choice structure:** Customers choose bucket first, then product
- **Cross-bucket substitution:** Significant elasticity between buckets
- **Value-based pricing:** Price decoupled from physical attributes (0.34 correlation)
- **Sequential patterns:** Primary → complementary purchases for the 3% who return

**5. Expected CLV Analysis (PROPENSITY-WEIGHTED):**
- High-propensity customers: 171 BRL expected CLV
- Low-propensity customers: 80 BRL expected CLV
- **Implication:** Segment strategies by propensity, not blanket loyalty programs

---

**Slides 7-9: Strategic Recommendations (REVISED)**

**RECOMMENDATION 1: Acquisition Excellence (Not Retention)**

**Reality Check:**
- 96.9% buy once → optimize THAT transaction
- Only Fashion (7.2%) and Food (6.1%) show meaningful repeat rates

**Strategy:**
- Competitive pricing on high-traffic products (acquire customers)
- Maximize first-purchase value through cross-sell
- Invest in retention ONLY for high-propensity segment + Fashion/Food categories

**Expected Impact:** +10-12% first-purchase value

---

**RECOMMENDATION 2: Cross-Sell Optimization (Same-Order Value)**

**Insight:** Can't rely on future purchases → maximize initial basket

**Strategy:**
- Data-driven bundle recommendations
- "Complete the set" checkout optimization
- Loss leader products driving basket additions

**Implementation:**
- HOME_ESSENTIALS: "Complete the room" bundles (bed + bedding + decor)
- LEISURE_LIFESTYLE: "Gift ready" packages (product + wrap + card)
- PERSONAL_CARE: "Starter kits" (discovery set pricing)

**Expected Impact:** +15-20% AOV, +$680K annually

---

**RECOMMENDATION 3: Sequential Journey Optimization**

**Insight:** Repeat customers (3%) follow primary → complementary patterns

**Strategy:**
- Competitive pricing on primary products (bed frames, premium skincare)
- Margin extraction from complementary products (bedding, refills)
- Targeted campaigns after primary purchase (high-propensity customers only)

**Implementation:**
- Reduce primary product margin 5-10%
- Increase complementary product margin 10-15%
- Email retargeting for high-propensity buyers

**Expected Impact:** +8% net revenue, +$264K annually

---

**RECOMMENDATION 4: Bucket-Specific Pricing Strategies**

**LEISURE_LIFESTYLE (Priority #1):**
- Dynamic pricing (high elasticity = -1.8)
- Bundle optimization
- Promotional strategies
- **Why:** 29% revenue, discretionary spending, highest optimization potential

**HOME_ESSENTIALS (Volume Leader):**
- Sequential journey focus
- "Complete the room" bundles
- Loss leader strategies
- **Why:** 31% orders, clear primary-complementary patterns

**PERSONAL_CARE (Brand Loyalty):**
- Premium positioning (low elasticity = -0.6)
- Starter kit → refill pricing
- Subscriptions for high-propensity customers
- **Why:** Brand preferences reduce price sensitivity

**ELECTRONICS (One-Time):**
- Pure acquisition pricing
- Competitive benchmarking
- No retention investment
- **Why:** 3.6% repeat rate, quality-driven purchases

**FASHION/FOOD (Retention Focus):**
- These are ONLY categories where loyalty programs make sense
- Subscriptions, seasonal campaigns, personalized offers
- **Why:** 7.2% and 6.1% repeat rates (2x overall average)

---

**RECOMMENDATION 5: Propensity-Based Customer Management**

**Strategy:**
- Predict which customers will repeat (propensity model)
- Different strategies by segment

**High-Propensity Segment (20% of customers):**
- Retention investment justified
- Email campaigns, personalized offers
- Post-purchase follow-up
- Expected CLV: 171 BRL

**Low-Propensity Segment (80% of customers):**
- Maximize first-purchase value
- Aggressive checkout cross-sell
- No retention spending
- Expected CLV: 80 BRL (first purchase only)

**Expected Impact:** +$144K from better targeting, 2.9x ROI on retention investment

---

**Slide 10: Expected Business Impact (UPDATED)**

**Financial Impact:**
- **GMV increase: +12-15% (+$1.6-1.9M)**
- **Average Order Value: +15-20%** (basket optimization)
- **Margin improvement: +5-7%** (mix optimization)
- **Expected CLV increase: +18-22%** (propensity-weighted)

**Operational Changes:**
- Shift from retention to acquisition + same-order cross-sell
- Implement propensity-based targeting
- Dynamic pricing in LEISURE_LIFESTYLE
- Sequential journey campaigns for HOME_ESSENTIALS

**Risk Mitigation:**
- Phased rollout (pilot in LEISURE_LIFESTYLE first)
- A/B testing before full deployment
- Customer feedback monitoring
- Competitive response tracking

---

**Slide 11: Implementation Roadmap**

**3-Month Pilot Plan (REVISED):**

**Month 1: Cross-Sell Optimization**
- Implement checkout bundle recommendations
- A/B test in LEISURE_LIFESTYLE (highest revenue)
- Target: +10% basket size
- Success metrics: AOV increase, conversion rate impact

**Month 2: Sequential Journey Launch**
- Competitive pricing on primary products (HOME_ESSENTIALS)
- Email campaigns for complementary products (high-propensity only)
- Target: +8% net revenue in HOME_ESSENTIALS
- Success metrics: Complementary purchase conversion, margin improvement

**Month 3: Propensity-Based Targeting**
- Deploy propensity model
- Segment customers in real-time
- Differentiated retention investment (high-propensity only)
- Target: 2.5x ROI on retention spending
- Success metrics: Repeat rate in high-propensity segment

**6-Month Full Rollout:**
- Expand dynamic pricing (LEISURE_LIFESTYLE → other buckets)
- Roll out Fashion/Food subscription models
- Implement loss leader strategies
- Platform-wide bundle recommendations

---

**Slide 12: Success Metrics & Monitoring (UPDATED)**

**Primary KPIs:**
- Average Order Value (AOV) - target: +15-20%
- Items per order (basket size) - target: +25-30%
- First-purchase value by propensity segment
- Expected CLV (propensity-weighted)
- Margin % by bucket

**Secondary KPIs:**
- Customer acquisition cost (CAC)
- Conversion rate by pricing strategy
- Bundle attachment rate
- Sequential purchase conversion (for 3% repeaters)
- Propensity model accuracy

**Guardrail Metrics:**
- Customer satisfaction scores
- Review ratings
- Return/cancellation rates
- Competitive price positioning

**NOT Focusing On (Reality-Based):**
- Overall repeat purchase rate (unrealistic to improve significantly)
- Customer retention rate (wrong metric for this marketplace)
- Long-term CLV growth (insufficient repeat data)

**Dashboard Requirements:**
- Real-time AOV and basket size tracking
- Propensity segment performance
- Bucket-level revenue and margin
- Cross-sell and bundle effectiveness
- Sequential journey conversion funnels

---

**Slide 13: Resource Requirements**

**Team:**
- Data science lead (pricing optimization, modeling)
- Analytics engineer (dashboard, experimentation platform)
- Product manager (cross-functional coordination)
- Marketing lead (email campaigns, retargeting)

**Technology:**
- Pricing engine/algorithm (dynamic pricing for LEISURE_LIFESTYLE)
- Experimentation platform (A/B testing infrastructure)
- Propensity model deployment (real-time scoring)
- Recommendation system (cross-sell, bundles)

**Timeline:**
- 3 months pilot → data collection and iteration
- 6 months full rollout → platform-wide deployment
- Ongoing optimization and monitoring

**Investment:**
- Technology: $150-200K
- Team: $300-400K annually
- Expected ROI: 5-7x in Year 1

---

**Slide 14: Risk Assessment (UPDATED)**

**Risk 1: Customer backlash to dynamic pricing**
- Mitigation: Gradual rollout, transparency, A/B testing
- Probability: Low | Impact: Medium

**Risk 2: Cross-sell perceived as pushy**
- Mitigation: Data-driven recommendations, opt-out options
- Probability: Low | Impact: Low

**Risk 3: Seller resistance to margin changes**
- Mitigation: Show revenue upside from volume increase
- Probability: Medium | Impact: Medium

**Risk 4: Propensity model inaccuracy**
- Mitigation: Conservative thresholds, ongoing calibration
- Probability: Medium | Impact: Low

**Risk 5: Competitive response**
- Mitigation: Monitor competitor pricing, adjust strategies
- Probability: High | Impact: Medium

**Risk 6: Implementation complexity**
- Mitigation: Phased approach, technical support, training
- Probability: Medium | Impact: Medium

**Overall Risk Profile:** Medium (manageable with proper execution)

---

**Slide 15: Next Steps & Decision**

**Immediate Actions:**
1. Executive approval and budget allocation
2. Assemble core team (data science, product, marketing)
3. Technology vendor selection (pricing engine, experimentation platform)
4. Pilot scope finalization (confirm LEISURE_LIFESTYLE as starting point)

**Decision Points:**
- Approve 3-month pilot budget ($100-150K)
- Commit team resources (3-4 FTE)
- Set success criteria for pilot continuation

**Timeline:**
- Week 1-2: Team assembly, vendor selection
- Month 1: Pilot launch (cross-sell optimization)
- Month 2: Sequential journey implementation
- Month 3: Propensity-based targeting
- Month 4: Pilot results review and go/no-go decision
- Month 5-6: Full rollout preparation

**Expected Decision Date:** [Insert date]

**Contact for Questions:** [Your name/contact]

---

### 2. Power BI Dashboard (UPDATED STRUCTURE)

**Interactive Visualizations:**

**Overview Tab (REVISED):**
- GMV trend (not just total, but breakdown by acquisition vs repeat)
- Average Order Value (AOV) - primary metric
- Items per order (basket size)
- First-purchase value by propensity segment
- Margin % by bucket

**Segmentation Tab (REVISED):**
- Propensity-based segments (high/medium/low)
- First-purchase behavioral segments
- Geographic patterns
- Expected CLV distribution (propensity-weighted)

**Pricing Tab:**
- Bucket-level elasticity heatmap
- Elasticity curves (interactive by bucket)
- Optimal vs current pricing by bucket
- Dynamic pricing performance (LEISURE_LIFESTYLE)

**Cross-Sell & Basket Tab (NEW):**
- Bundle attachment rates
- Items per order trends
- Most effective product combinations
- Sequential purchase conversion funnels
- Cross-sell revenue attribution

**Marketplace Tab:**
- Seller performance metrics
- Price dispersion visualization
- Platform commission scenario modeling
- Quality-price relationship

**Geographic Tab:**
- State-level performance map
- Shipping cost impact by region
- Regional pricing recommendations

**Filters:**
- Date range
- Product bucket
- Customer propensity segment
- Geographic region
- Seller tier

---

### Deliverables - Week 5

1. **Executive Deck PDF:** `Olist_Pricing_Transformation_Executive_Deck_REVISED.pdf`
   - 15 slides, consultant-quality
   - Updated findings from Week 1
   - Revised recommendations (acquisition + cross-sell focus)
   - Realistic impact projections

2. **Power BI Dashboard:** `Olist_Dashboard_REVISED.pbix`
   - Updated metrics (AOV, basket size prioritized)
   - Propensity segment tracking
   - Cross-sell performance monitoring

3. **Slide Deck Speaker Notes**
   - Talking points for each slide
   - Anticipated questions and answers
   - Backup slides (detailed analysis)

---

## WEEK 6: POLISH, PACKAGE & LAUNCH (UPDATED)

### 1. Code Repository Cleanup

**Organize GitHub:**
```
ecommerce-pricing-transformation/
├── README.md (comprehensive overview with Week 1 findings)
├── CHANGELOG.md (document roadmap revisions and why)
├── data/
│   └── README.md (data sources, download instructions)
├── notebooks/
│   ├── 01_data_exploration_business_context.ipynb ✅
│   ├── 02_segmentation_elasticity_clv_REVISED.ipynb
│   ├── 03_seller_marketplace_dynamics_REVISED.ipynb
│   └── 04_optimization_clv_scenarios_REVISED.ipynb
├── src/
│   ├── data_processing.py (reusable functions)
│   ├── modeling.py (elasticity, clustering, propensity model)
│   ├── nested_logit.py (bucket-product choice model)
│   └── visualization.py (plotting functions)
├── outputs/
│   ├── executive_deck_REVISED.pdf
│   ├── technical_report_REVISED.pdf
│   └── figures/ (saved visualizations)
├── requirements.txt
└── LICENSE
```

**Documentation Updates:**
- README with:
  - Project overview
  - **Week 1 key discoveries and roadmap revisions**
  - Business problem (updated with 3.12% repeat rate reality)
  - Key findings (product mix shift, nested choice, etc.)
  - How to reproduce
  - Technologies used
- CHANGELOG documenting strategic pivots
- Comments in all notebooks explaining analytical choices
- Docstrings for functions
- Data dictionary

---

### 2. Technical Report (UPDATED - 15-20 pages)

**1. Introduction (2 pages)**
- Business context
- Objectives
- **Data overview and Week 1 discoveries**

**2. Methodology (5-6 pages) - UPDATED**
- Data preprocessing
- **First-purchase segmentation** (not traditional RFM)
- **Propensity to repeat modeling** (classification approach)
- **Nested logit model** (bucket → product choice)
- Elasticity estimation (log-log regression specifications)
- **Expected CLV calculation** (propensity-weighted)
- Optimization framework (AOV, margin, expected CLV)

**3. Results (6-7 pages) - UPDATED**
- **Customer reality:** 3.12% repeat rate, implications
- Propensity model results (accuracy, feature importance)
- Nested logit estimates (choice parameters, substitution patterns)
- Elasticity estimates by bucket (tables, confidence intervals)
- **Product mix shift analysis** (primary → complementary)
- **Expected CLV by propensity segment**
- Cross-sell and basket optimization results

**4. Business Implications (2-3 pages) - REVISED**
- Strategic recommendations (acquisition + cross-sell focus)
- Implementation considerations
- Expected impact (GMV, AOV, margin)
- **Why traditional loyalty programs won't work**

**5. Robustness & Limitations (1-2 pages)**
- Sensitivity analyses
- Data limitations (no platform identity, no brand data)
- Model assumptions
- **Caveats about low repeat rate affecting certain analyses**

**6. Appendices**
- Additional tables
- Code snippets (propensity model, nested logit)
- Mathematical derivations
- Supplementary visualizations

---

### 3. LinkedIn Launch Post (UPDATED)

**Post Structure:**

**Hook:**
> "I just completed a 6-week e-commerce pricing transformation project. The biggest surprise? 96.9% of customers buy exactly once. Here's what that means for pricing strategy..."

**Context (2-3 paragraphs):**
> "Using 100K+ transactions from a Brazilian marketplace, I analyzed customer behavior, estimated demand elasticities, and built optimization models for revenue growth.
>
> The conventional wisdom says focus on customer retention and loyalty programs. But the data told a different story: only 3.12% of customers ever made a second purchase. This is an acquisition marketplace, not a retention platform.
>
> This finding completely changed my analytical approach."

**Key Findings (bullets):**
> "Three critical insights emerged:
>
> 1. **The 'repeat discount' myth:** Repeat customers appear to pay 15% less, but it's not bargain hunting—it's product mix shift. They buy expensive primary products first (bed frame), then cheaper complementary items (bedding). 90% of same-SKU repurchases happened at identical prices.
>
> 2. **Nested decision-making:** Customers choose a product bucket first (LEISURE_LIFESTYLE, HOME_ESSENTIALS), then select products within it. This bucket-level choice has massive implications for cross-category pricing strategies.
>
> 3. **Propensity matters more than history:** With 97% one-time buyers, predicting WHO will repeat is more valuable than analyzing who already has. I built a propensity model showing expected CLV varies 2x between high and low-propensity segments."

**Strategic Recommendations (paragraph):**
> "The analysis led to acquisition-focused strategies: optimize first-purchase value through cross-sell (+15-20% AOV), implement sequential journey pricing (competitive primaries, high-margin accessories), and invest in retention ONLY for high-propensity customers. Dynamic pricing in high-elasticity categories (LEISURE_LIFESTYLE) showed +7% revenue potential."

**Results (bullets):**
> "Modeling showed potential for:
> • 12-15% GMV increase
> • 15-20% average order value improvement  
> • 5-7% margin expansion
> • 2.9x ROI on targeted retention investment"

**Technical Approach (paragraph):**
> "I applied nested logit models for choice analysis, propensity modeling for segmentation, discrete choice elasticity estimation, and multi-objective optimization. The project combines PhD-level econometric rigor with practical business strategy—complete with executive deck and implementation roadmap."

**Methodology Note:**
> "The most important lesson: let data challenge your assumptions. I started expecting to recommend loyalty programs. The data said focus on acquisition and same-order cross-sell instead. Being willing to pivot based on evidence is what separates analysis from insight."

**Call to Action:**
> "Full project on GitHub: [link]  
> Executive deck, technical report, and code included.
>
> Have you seen similar patterns in marketplaces? What's been your experience with low-repeat-rate businesses?
>
> #PricingStrategy #DataScience #Economics #Ecommerce #RevenueOptimization"

**Visuals (attach 2-3):**
- Repeat rate chart (showing 96.9% one-time)
- Nested choice diagram (bucket → product)
- Elasticity comparison by bucket

---

### 4. Portfolio Website Update

**Add Project Page:**

**Title:** "E-Commerce Pricing Transformation: When 97% Buy Only Once"

**Subtitle:** "How low repeat rates change everything about pricing strategy"

**Overview (2-3 paragraphs):**
- Business problem (Brazilian marketplace, low retention)
- Analytical approach (segmentation, elasticity, optimization)
- Key pivot (discovering 3.12% repeat rate changed entire strategy)

**Key Findings Section:**
- Repeat rate reality and implications
- Product mix shift vs bargain hunting
- Nested choice structure
- Propensity-based segmentation

**Visualizations (4-5 charts):**
- Repeat purchase distribution
- Bucket-level elasticities
- Propensity model feature importance
- Sequential purchase flow
- Expected impact waterfall

**Business Impact Summary:**
- +12-15% GMV
- +15-20% AOV
- +5-7% margin
- Strategic recommendations

**Technical Stack:**
- Python, pandas, statsmodels, scikit-learn
- Nested logit modeling
- Propensity modeling (classification)
- Power BI for dashboards

**Links:**
- GitHub repository
- Executive deck PDF
- Technical report PDF
- LinkedIn post

---

### 5. Final Quality Check

**Checklist:**
- All notebooks run end-to-end without errors
- GitHub README is comprehensive and explains roadmap revisions
- CHANGELOG.md documents what changed from original plan and why
- Executive deck reflects Week 1 findings (no outdated RFM references)
- Power BI dashboard has updated metrics (AOV, basket size priority)
- Technical report explains analytical pivots
- LinkedIn post tells compelling "data challenged assumptions" story
- All links work
- Consistent terminology (propensity-weighted CLV, not realized CLV)
- No references to traditional loyalty programs as main recommendation

**Cross-Check Consistency:**
- Executive deck, technical report, and notebooks all tell same story
- Metrics consistent across all documents
- Recommendations aligned with data findings
- Impact estimates match across presentations

---

### Deliverables (Final - UPDATED)

1. **GitHub Repository** (public, fully documented)
   - Includes CHANGELOG.md explaining roadmap evolution
   - README highlights key discoveries that changed approach

2. **Executive Deck PDF** (consultant-quality, 15 slides)
   - Updated with acquisition-focused strategies
   - Realistic about repeat rate constraints

3. **Technical Report PDF** (15-20 pages)
   - Explains propensity modeling and nested logit
   - Discusses analytical pivots and why

4. **Power BI Dashboard** (.pbix file + screenshots)
   - Prioritizes AOV, basket size, propensity segments
   - Cross-sell performance tracking

5. **LinkedIn Post** (published, with engagement)
   - Compelling narrative about data challenging assumptions
   - "97% buy once" hook

6. **Portfolio Website** (project page live)
   - Highlights strategic thinking and adaptability

---

## PROJECT COMPLETION CHECKLIST (UPDATED)

**By April 4, you will have:**

**Analytical Depth:**
- First-purchase behavioral segmentation (not traditional RFM)
- Propensity to repeat modeling (classification)
- Nested logit model (bucket → product choice)
- Price elasticity by bucket with substitution patterns
- Expected CLV calculation (propensity-weighted)
- Sequential purchase analysis (primary → complementary)
- Cross-sell and basket optimization
- 6+ scenario simulations with impact quantification

**Business Value:**
- Executive recommendation deck (C-suite ready, updated findings)
- Implementation roadmap (3-month pilot, acquisition-focused)
- Expected impact quantified (GMV, AOV, margin)
- Realistic about market constraints (3.12% repeat rate)
- Risk assessment & mitigation

**Technical Excellence:**
- Clean, documented code (4 notebooks, revised based on findings)
- Reproducible analysis
- Professional visualizations
- Power BI dashboard (updated metrics)
- CHANGELOG documenting analytical evolution

**Consulting Positioning:**
- Structured thinking (MECE framework)
- Data-driven adaptation (pivoted based on Week 1 evidence)
- Business-first framing (not just academic exercise)
- Actionable recommendations (not generic advice)
- Implementation focus (pilot plan, success metrics)

**Portfolio Asset:**
- GitHub repository (showcases technical skills + strategic thinking)
- LinkedIn visibility (compelling narrative)
- Interview talking point (demonstrates adaptability and critical thinking)
- Proof of concept (can handle real-world complexity and ambiguity)

---

## INTERVIEW NARRATIVES (UPDATED)

### 30-Second Version (REVISED)

> "For my portfolio, I analyzed a Brazilian e-commerce marketplace with 100K+ transactions. I expected to recommend loyalty programs and retention strategies—until Week 1 data revealed only 3.12% of customers ever return.
>
> That finding changed everything. I pivoted to acquisition excellence and same-order cross-sell optimization. Using nested logit models and propensity-based segmentation, I developed strategies to increase average order value by 15-20% and GMV by 12-15%.
>
> The project demonstrates both technical rigor—econometric modeling, elasticity estimation—and strategic adaptability. I packaged it as an executive deck and implementation roadmap showing how data-driven insights drive measurable impact."

---

### 5-Minute Version (REVISED)

**Setup (30 sec):**
> "I built an end-to-end pricing transformation project for an e-commerce marketplace—specifically designed to showcase both analytical depth and business strategy thinking. I chose this domain because it combines customer behavior, competitive dynamics, and platform economics."

**The Pivot (1 min):**
> "Here's where it got interesting. I started with a traditional framework: RFM segmentation, loyalty programs, customer lifetime value optimization—the standard approach to e-commerce.
>
> Then Week 1 data hit: 96.9% of customers buy exactly once. Only 3.12% ever return. I'm looking at an acquisition marketplace, not a retention platform. Traditional loyalty programs would be throwing money away.
>
> That discovery forced a complete strategic pivot. Instead of optimizing repeat purchases, I focused on: one, maximizing first-purchase value through cross-sell; two, predicting which customers WILL repeat and targeting only them; and three, optimizing sequential journeys for the rare customers who do come back."

**Analytical Approach (2 min):**
> "I built three key models to address this reality:
>
> First, a nested logit framework capturing how customers actually shop: they choose a product bucket first—like LEISURE_LIFESTYLE or HOME_ESSENTIALS—then select products within that bucket. This revealed cross-bucket substitution patterns that aren't visible in standard elasticity models.
>
> Second, a propensity-to-repeat classification model predicting which first-time buyers will become part of that 3%. This showed expected customer lifetime value varies by 2x between high and low-propensity segments. Now we know WHERE to invest in retention—and where not to.
>
> Third, I estimated demand elasticities by bucket, finding LEISURE_LIFESTYLE is highly elastic at -1.8, while PERSONAL_CARE shows brand loyalty at -0.6. This guided bucket-specific pricing strategies.
>
> I also discovered what looked like 'repeat customer discounts' were actually product mix shifts: customers buy expensive primary products first—bed frames, premium skincare—then cheaper complementary items—bedding, refills. When I isolated same-SKU repurchases, 90% paid identical prices. No bargain hunting at all."

**Business Impact (1 min):**
> "The revised strategy focuses on three levers:
>
> Cross-sell optimization—bundle recommendations at checkout to increase basket size 15-20%. Sequential journey pricing—competitive on primary products to acquire customers, extract margin from complementary products. Propensity-based targeting—invest in retention only for the predicted 20% who'll actually return, achieving 2.9x ROI.
>
> Scenario modeling shows 12-15% GMV upside with 5-7% margin improvement. I packaged this as both an executive deck showing the 3-month pilot roadmap, and full technical implementation with code."

**Close (30 sec):**
> "What I'm most proud of: the project demonstrates strategic thinking under uncertainty. I could have ignored the 3% repeat rate and built a beautiful but irrelevant retention model. Instead, I let data challenge my assumptions and built strategies for the market reality, not the market I wished existed.
>
> That's the mindset I bring to pricing work: rigorous analysis grounded in evidence, with the flexibility to pivot when data points a different direction."

---

## KEY ANALYTICAL PIVOTS SUMMARY

**What Changed and Why:**

| Original Plan | Week 1 Finding | Strategic Pivot |
|--------------|----------------|-----------------|
| Traditional RFM segmentation | Only 3.12% repeat rate | First-purchase segmentation + propensity modeling |
| Focus on repeat purchase optimization | 96.9% buy once | Focus on acquisition + same-order cross-sell |
| Generic elasticity analysis | 10 strategic buckets created | Nested logit model (bucket → product choice) |
| Loyalty program recommendations | Repeat customers aren't bargain hunters | Sequential journey optimization (primary → complementary) |
| Realized CLV calculation | Insufficient repeat data | Expected CLV (propensity-weighted) |
| Blanket retention investment | Most won't return | Targeted retention (high-propensity + Fashion/Food only) |

**Lessons Demonstrated:**
- Data-driven decision making (changed course based on evidence)
- Strategic flexibility (pivoted from original plan)
- Business realism (recommendations match market reality)
- Technical sophistication (nested logit, propensity modeling)
- Consultant mindset (actionable insights, not just analysis)

---

## TECHNOLOGIES USED (UPDATED)

**Programming:**
- Python 3.9+
- Jupyter Notebook

**Data Analysis:**
- pandas
- numpy
- scipy

**Modeling:**
- statsmodels (elasticity estimation, nested logit)
- scikit-learn (propensity modeling, clustering)
- xgboost (propensity classification - optional)

**Visualization:**
- matplotlib
- seaborn
- plotly
- Power BI

**Tools:**
- Git/GitHub
- VS Code
- PowerPoint/Google Slides

---

## REVISED TIMELINE

**PROJECT START DATE:** February 23, 2026 (Week 1 completed)  
**WEEK 1 COMPLETED:** February 25, 2026  
**REVISED PROJECT COMPLETION TARGET:** April 4, 2026  
**TOTAL DURATION:** 6 weeks

**Week-by-Week:**
- Week 1: Feb 23-25 (Data exploration, business context) - DONE
- Week 2: March 2-6 (Segmentation, nested logit, elasticity) - REVISED
- Week 3: March 9-13 (Marketplace dynamics, cross-sell analysis) - REVISED
- Week 4: March 16-20 (Optimization, scenarios) - REVISED
- Week 5: March 23-27 (Executive deck, dashboard) - UPDATED
- Week 6: March 30 - April 4 (Polish, package, launch) - FINAL

**LinkedIn Launch Target:** April 7, 2026  
**Job Application Start:** May-June 2026 (when EAD arrives)

---

## FINAL NOTES

**This revised roadmap reflects:**
1. Actual findings from Week 1 data exploration
2. Strategic pivots based on evidence (3.12% repeat rate)
3. Enhanced analytical approaches (nested logit, propensity modeling)
4. Realistic business recommendations (acquisition + cross-sell focus)
5. Maintained timeline (6 weeks total, on track)

**The revision demonstrates:**
- Critical thinking (questioned original assumptions)
- Data-driven adaptability (changed course based on evidence)
- Business acumen (recommendations fit market reality)
- Technical sophistication (added nested logit, propensity models)
- Consulting mindset (actionable insights, not just academic analysis)
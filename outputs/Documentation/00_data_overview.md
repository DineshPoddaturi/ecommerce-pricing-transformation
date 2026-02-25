# Data

## Dataset Source
The analysis used the **Olist Brazilian E-Commerce Dataset**, an open-source dataset available on [Kaggle](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce).

**Olist** is a Brazilian e-commerce marketplace aggregator that connects small and medium-sized businesses with major marketplaces (similar to how Fulfillment by Amazon works). Sellers list their products on Olist's platform, and Olist distributes these listings across multiple Brazilian marketplaces.

## How Olist Works

### For Sellers
- Small/medium businesses list products on Olist's centralized platform
- Olist pushes these listings to major Brazilian marketplaces simultaneously
- Sellers manage inventory centrally, sell across multiple channels
- Sellers pay commission to Olist per sale
- Sellers receive notifications when a product is sold
- Sellers hand over an item to the logistic carriers for fulfillment

### For Marketplace
- Platforms like Mercado Livre, B2W, Via Varejo get access to thousands of sellers
- Olist handles seller onboarding, quality control, logistics coordination
- Marketplaces earn through their own commission structures

### For Consumers
- Shop on familiar platforms (Mercado Livre, Amazon Brazil, etc.)
- Don't interact with Olist directly
- See products from Olist sellers alongside native marketplace listings
- Purchase products via marketplace checkout
- Receive orders
- Leave a review about the order

## Market Structure

### Primary Distribution Channels

Where Olist sellers' products appear:
- **Mercado Livre** - Latin America's largest e-commerce platform
- **B2W Digital** - Conglomerate operating Americanas.com, Submarino, Shoptime
- **Grupo Casas Bahia (previously Via Varejo)** - Operates Casas Bahia, Ponto Frio
- **Amazon Brazil** - Amazon's Brazilian marketplace
- Other regional platforms

### Important Data Limitation: Platform Identity Not Recorded

**Transaction structure:**
The dataset contains individual transaction records at the order-item level. Each order occurred on one specific marketplace, but platform identity is not recorded in the data.

**What we observe:** 
- Observed - Customer ID, product ID, seller ID, order ID, price, quantity, timestamp
- Not observed - Which platform the transaction occurred on

**Implication for Analysis**
When estimating the demand elasticity, we pool transactions across all platforms. Elasticity estimates represent consumer price sensitivity aggregated across channels, not platform-specific demand.

## Competitive Dynamics

### Seller Competition
- Multiple sellers can list the same or similar product through Olist
- Sellers compete on price, shipping speed, and reviews
- Same product may appear at different prices from different sellers
- Creates natural price variation useful for elasticity estimation

### Platform Effects (not observable)
- Consumers choose platforms based on trust, habit, loyalty programs
- Platform algorithms determine product visibility and ranking
- Each platform may have different commission structures affecting seller pricing incentives
- These effects are not separately identifiable in the data

## Dataset Structure

The dataset consists of 9 interconnected tables spanning September 2016 - August 2018:

![Olist Database Schema](../data/olist_data_schema.png)
*Figure: Olist dataset entity-relationship diagram (Source: Kaggle)*

### Core Tables

#### 1. Orders (`olist_orders_dataset.csv`)
- **Records:** 99,411 orders
- **Key Fields:**
    - `order_id` - Unique order identifier
    - `customer_id` - Customer id linking the customer table
    - `order_status` - Order lifecycle (delivered, shipped, cancelled, etc.)
    - `order_purchase_timestamp` - When the order was placed
    - `order_delivered_customer_date` - When customer received order
- **Purpose:** Transaction timeline and order lifecycle tracking. This is the core dataset containing information of each order.
- **Note:** Each order can contain multiple items (one-to-many relationship with order_items)

#### 2. Order Items (`olist_order_items_dataset.csv`)
- **Records:** 112,650 line items
- **Key Fields:**
    - `order_id` - Order id linking the orders table
    - `product_id` - Product id linking the products table
    - `seller_id` - Seller id linking the sellers table
    - **`price`** - Product list price (BRL)
    - **`freight_value`** - Shipping cost (BRL)
    - `order_item_id` - Item sequence within order
- **Purpose:** Core table for pricing analysis. This table contains the product prices and quantities. This dataset contains the data about the items purchased within each order.
- **Note:** This is the unit of analysis for elasticity estimation

#### 3. Products (`olist_products_dataset.csv`)
- **Records:** 32,951 unique products
- **Key Fields:**
    - `product_id` - Unique product identifier
    - `product_category_name` - Category (Portuguese)
    - `product_weight_g` - Weight in grams
    - `product_length_cm`, `product_height_cm`, `product_width_cm` - Dimensions
- **Purpose:** Product attributes and physical characteristics
- **Limitation:** No product names, descriptions, or brand information

#### 4. Customers (`olist_customers_dataset.csv`)
- **Records:** 99,411 customers
- **Key Fields:**
    - `customer_id` - Unique identifier per order
    - `customer_unique_id` - Unique identifier per customer (tracks repeat purchases)
    - `customer_zip_code_prefix` - Postal code prefix (links to geolocation via `geolocation_zip_code_prefix`)
    - `customer_city` - City location
    - `customer_state` - State abbreviation (SP, RJ, MG, etc.)
- **Purpose:** Geographic segmentation and repeat purchase analysis
- **Note:** `customer_id` is unique per order; `customer_unique_id` tracks same customer across orders.

#### 5. Sellers (`olist_sellers_dataset.csv`)
- **Records:** 3,095 unique sellers
- **Key Fields:**
    - `seller_id` - Unique seller identifier
    - `seller_city` - Seller location (city)
    - `seller_state` - Seller location (state)
    - `seller_zip_code_prefix` - Seller postal code (links to geolocation)
- **Purpose:** Analyze seller competition and geographic shipping patterns
- **Note:** Multiple sellers can offer same/similar products

### Supporting Tables

#### 6. Reviews (`olist_order_reviews_dataset.csv`)
- **Records:** 99,224 reviews
- **Key Fields:**
    - `review_id` - Unique review identifier
    - `order_id` - Links to orders
    - `review_score` - Rating (1-5 stars) - **100% complete**
    - `review_comment_title` - Review title - **Only 11.7% complete (11,568/99,224)**
    - `review_comment_message` - Review text (Portuguese) - **Only 41.3% complete (40,977/99,224)**
    - `review_creation_date` - When review was written
    - `review_answer_timestamp` - When review was answered by seller
- **Purpose:** Quality signals and customer satisfaction metrics
- **Data Quality Note:** Most reviews contain only numeric scores; text comments are sparse

#### 7. Product Categories (`product_category_name_translation.csv`)
- **Records:** 71 categories
- **Key Fields:**
    - `product_category_name` - Portuguese category name
    - `product_category_name_english` - English translation
- **Purpose:** Translate category labels for analysis
- **Examples:** bed_bath_table, health_beauty, sports_leisure, electronics

#### 8. Geolocation (`olist_geolocation_dataset.csv`)
- **Records:** 1,000,163 geographic coordinates
- **Key Fields:**
    - `geolocation_zip_code_prefix` - Postal code prefix
    - `geolocation_lat` - Latitude coordinate
    - `geolocation_lng` - Longitude coordinate
    - `geolocation_city` - City name
    - `geolocation_state` - State abbreviation
- **Purpose:** Calculate shipping distances and map customer/seller locations
- **Note:** Multiple coordinates per zip code (neighborhoods within same postal area)

#### 9. Order Payments (`olist_order_payments_dataset.csv`)
- **Records:** 103,886 payment records
- **Key Fields:**
    - `order_id` - Links to orders (FOREIGN KEY)
    - `payment_sequential` - Payment sequence number within order
    - `payment_type` - Payment method (credit_card, boleto, voucher, debit_card)
    - `payment_installments` - Number of installments (Brazilian consumer credit feature)
    - `payment_value` - Payment amount in BRL
- **Purpose:** Payment behavior and installment analysis
- **Note:** Orders can have multiple payment records (split payments across methods)
**Important:** 
- 103,886 payment records for 99,441 orders indicate approximately 4.5% of the orders use multiple payment methods.
- `payment_sequential` = 1 for first payment, 2 for second, etc.
- Total order value = sum of all `payment_value` for that `order_id`

## Data Scope

### Temporal Coverage
- **Full Period:** September 2016 - August 2018 (24 months)
- **Analysis Period:** January 2017 - August 2018 (20 months of stable data)
- **Excluded:** September 2016 (incomplete) and September 2018 (data cutoff)
- **Peak Volume:** November 2017 (8,665 orders - Black Friday/holiday season)

### Transaction Scale
- **Orders:** 99,441
- **Line Items:** 112,650
- **Unique Products:** 32,951
- **Unique Sellers:** 3,095
- **Average Items per Order:** 1.13 (mostly single-item purchases)
- **Payment Records:** 103,886 (~4.5% of orders use split payments)
- **Reviews:** 99,224 (99.8% coverage of orders)

### Geographic Coverage
- **Primary Markets:** São Paulo (SP), Rio de Janeiro (RJ), Minas Gerais (MG)
- **Total Coverage:** 27 Brazilian states
- **Geographic Coordinates:** 1M+ lat/lng records for precise distance calculation
- **Distribution:** Concentrated in Southeast Brazil (urban markets)

### Price Distribution
- **Mean Price:** 120.65 BRL (~$30 USD at 2017 exchange rates)
- **Median Price:** 74.99 BRL
- **Range:** 0.85 BRL - 6,735 BRL
- **Standard Deviation:** 183.63 BRL
- **Pattern:** Right-skewed distribution (most products under 200 BRL)
- **High-Value Items:** 844 products priced above 1,000 BRL (electronics, appliances)
- **Low-Value Items:** 117 products priced below 5 BRL (accessories, small items)

### Freight Costs
- **Mean Freight:** 19.99 BRL
- **Median Freight:** 16.26 BRL
- **Range:** 0 - 409.68 BRL
- **Standard Deviation:** 15.80 BRL
- **Pattern:** Banded pricing structure (standardized tiers at ~15, ~30, ~45, ~60 BRL)
- **Correlation with Price:** 0.41 (moderate positive correlation)
- **Correlation with Weight:** 0.61 (strong correlation - logistics-driven)

### Data Quality
- **Completeness:** 100% for all critical pricing fields (price, freight_value, order_id, product_id)
- **Review Scores:** 99.8% complete (99,224/99,441 orders)
- **Review Text:** Only 41% complete (sparse qualitative data)
- **Missing Values:** None in core transactional data
- **Anomalies:** Identified and documented (extreme prices, high freight costs - verified as legitimate)

## Why This Dataset for Pricing Analysis?

This marketplace structure creates ideal conditions for demand elasticity estimation:

### Strengths:
- **Natural Price Variation:** Seller competition creates price dispersion within categories
- **Multi-seller products:** Same products offered at different prices by competing sellers
- **Temporal changes:** Sellers adjust prices over time, creating variation
- **Large transaction volume:** 112,650 order items provide statistical power 
- **Stable pricing environment:** Minimal promotional noise (no aggressive Black Friday cycles)
- **Complete pricing data:** No missing values in critical fields  
- **Geographic breadth:** 27 Brazilian states with varying shipping costs
- **Quality signals:** Review scores available for 99.8% of orders 

### Limitations:
- **Platform aggregation:** Cannot separate Amazon vs Mercado Livre demand
- **No product descriptions:** Only category labels and physical attributes  
- **No brand information:** Cannot measure brand-specific elasticities
- **No marketing data:** No visibility into promotions or advertising 
- **Sparse review text:** Only 41% of orders have written comments

The above limitations are common in real-world marketplace data and require careful methodological choices, but do not prevent robust elasticity estimation.

## Methodological Implication: Platform Aggregation

**Critical Context:**

The dataset represents consumer transactions across multiple platforms (Mercado Livre, Amazon Brazil, B2W, Via Varejo), but platform identity is not recorded. We cannot distinguish which specific platform each transaction occurred on.

**What this means for analysis:**

Our elasticity estimates measure consumer price sensitivity aggregated across platforms, not platform-specific demand. This approach is analogous to:
- Grocery studies using scanner data aggregated across stores (Walmart + Target + Kroger)
- Airline pricing studies aggregating bookings across channels (airline.com + Expedia + Google Flights)
- Ride-sharing studies aggregating trips across app interfaces (iOS + Android + web)

**Why this approach is valid:**

Consumer demand elasticity can be estimated from aggregated transaction data as long as:
1. Price variation exists (seller competition, temporal changes)
2. Quantity responses are observable (individual transaction records)
3. Appropriate controls are included (product, seller, time fixed effects)

**What we measure:**
- How consumers respond to price changes in multi-platform marketplaces
- Substitution patterns between products within categories
- Price sensitivity across product buckets
- Aggregate demand elasticity (seller perspective)

**What we don't measure:**
- Platform-specific elasticities (Amazon vs Mercado Livre separately)
- Cross-platform substitution (consumers switching between marketplaces)
- Platform algorithm effects on visibility and demand

**Interpretation:** 

Results reflect the seller decision-making context in multi-platform distribution. Sellers using Olist price products once and observe aggregate demand across all channels. Our analysis answers: *"If I change my price, how does total consumer demand respond across all distribution platforms?"*

This framing is appropriate for sellers, marketplace aggregators, and pricing strategists operating in multi-channel e-commerce environments.
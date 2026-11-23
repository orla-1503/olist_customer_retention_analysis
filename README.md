# Olist e-commerce operations and customer retention analysis
> **Executive summary:** An operational and customer retention analysis of **115,370 delivered orders** on Brazil's Olist marketplace using Python. This project identifies why ~97% of customers purchase only once, pinpointing delivery delays exceeding a **3-day tolerance threshold** and interstate logistics bottlenecks rather than pricing or product mix and proposes a phased 6-month roadmap to improve SLA compliance and repeat purchase rates.

**Author:** Tran Ngoc Anh Minh (Orla) | **Tools:** Python (Pandas, NumPy, Matplotlib, Seaborn)  
🔗 **[View Executive Slide Deck (PDF)](./reports/Olist_Executive_Deck.pdf)** | 💻 **[View analysis notebook (Google Colab)](./notebooks/olist_eda_retention.ipynb)**


# 1. Business context and business questions
Olist is a leading Brazilian e-commerce platform infrastructure provider connecting SMBs with major marketplaces. While order volume and revenue expanded across 2017–2018, peak-season surges (Nov 2017 – Mar 2018) strained fulfillment capacity, tripling late-delivery rates, softening review scores, and leaving retention stuck at **2.46%**.
**Research questions:**
* How did operational scale fluctuate across phases, and at what point did order volume begin straining fulfillment?
* How do fulfillment and delivery bottlenecks impact customer review scores and churn/one-time purchase behavior?
* How does the first-order experience of one-time buyers differ from that of repeat buyers to ground actionable retention recommendations?

# 2. Dataset and methodology
### Data source
Sourced from the [Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) on Kaggle. 
- **Analytical scope:** Filtered from **119,143** raw orders (**96,096** customers) down to **115,370 delivered orders** (**93,091 unique customers**) between **Jan 2017 – Aug 2018** after removing incomplete 2016 trial records and missing delivery timestamps.
- The pipeline integrates six core relational tables (`orders`, `order_items`, `order_reviews`, `customers`, `sellers`, and `products`) across four analytical dimensions:
> - **Order performance:** Order value (Total_sales, AOV), shipping fees, and order status.
> - **Logistics:** Estimated vs. actual delivery timelines, and inter-state distances.
> - **Product:** Category mix and purchase lifecycle frequency.
> - **Customer feedback and retention:** Review scores (1–5 points) and repeat-purchase behavior.

### Feature engineering
- **Route classification (`route_type`):** Categorized shipments into **same** (`seller_state == customer_state`) and **different** (`seller_state != customer_state`).
- **Regional grouping (`region`):** Mapped Brazilian states into five macro-regions (`SE`, `S`, `NE`, `CW`, `N`) to uncover geographic bottlenecks. 
- **Delay fault attribution (`delivery_fault`):** Isolated responsibility across **Carrier fault**, **Seller fault** (missed handoff deadline), **Both**, or **No fault** by comparing actual carrier handoff and customer delivery timestamps against SLA limits.
- **Product frequency group:** Grouped product categories into **low-frequency (durable)** vs. **high-frequency (consumable)** tiers to test whether product type drives repeat buying.
  
### Tech stack
- **Data processing and analysis:** Python (`pandas`, `numpy`)
- **Data visualization:** `matplotlib`, `seaborn`
- **Querying and exploration:** Google Colab

# 3. Key insights
### 1) Growth Model & Peak-Season Strain
Olist's growth relies heavily on new-customer acquisition and modest basket values (AOV ~150–200 BRL) rather than repeat orders. Scaling volume rapidly without matching logistics capacity turns demand spikes into fulfillment bottlenecks.

<img width="100%" height="989" alt="growth and sales" src="https://github.com/user-attachments/assets/19a0f5c0-d1cf-4074-aad6-f6f6f8fe7ca8" />

<img width="100%" height="590" alt="average_review_and_operation_performance" src="https://github.com/user-attachments/assets/c30ce094-885e-4e8b-990c-dcbd4c2e147b" />

> **Takeaway:** Once monthly volume crosses ~7,000+ orders, late deliveries surge and average review scores dip, exposing capacity limits during peak demand.

### 2) Cross-State, Carrier & Regional Bottlenecks
Cross-state routes carry **1.78x** the volume of intra-state shipments, take roughly double the transit time (**14.10** vs. **7.02** days), and suffer higher delay rates. Carrier delays account for **77.7%** of late orders, with the **Northeast (`NE`)** experiencing the steepest friction (lowest cross-state rating at **3.90★** and ~6.0% repeat rate).

<img width="100%" height="690" alt="Delivery fault pareto and review score" src="https://github.com/user-attachments/assets/c3d69a12-a561-45e9-9577-2aa1abf02557" />
<img width="100%" height="690" alt="Regional and route type bottlenecks" src="https://github.com/user-attachments/assets/74e5296d-e357-4ce0-b0ea-52337156c71f" />

> **Takeaway:** Third-party carriers drive the bulk of late deliveries (`77.7%`), and long-haul cross-state routing amplifies dissatisfaction outside core hubs—especially in the Northeast (`NE`).

### 3) The 3-Day Delay Tolerance Cliff
Customers tolerate minor delays of 1–2 days reasonably well (**>3.0★**), but review scores drop sharply at **3 days late** (**~2.55★**) and fall below **2.0★** from **6+ days late**. Broken delivery promises damage satisfaction far more than normal transit duration or shipping-to-price ratios.

<img width="100%" alt="Delay Tolerance Cliff and Review Scores" src="https://github.com/user-attachments/assets/bbf1240c-bfdb-4873-aa01-72538dde9cfa" />

> **Takeaway:** Delay days (middle chart) drive a sharp satisfaction drop past the 3-day mark, whereas shipping duration (left) and freight-to-value ratio (right) play a secondary role.

### 4) First-Order Experience & Retention Drivers
First-order product mix (**81.2%** vs. **83.6%** durable goods) and baseline fulfillment quality are broadly similar across one-time and repeat buyers across all phases.

<img width="100%" height="490" alt="repurchased_rate_by_first_orders" src="https://github.com/user-attachments/assets/ff8c5026-5ddf-4693-9427-45a2751d8ee6" />

<img width="989" height="790" alt="product_frequency_group_by_first_order" src="https://github.com/user-attachments/assets/0dea1ccb-99e1-44c6-86c5-ab0feb2987e2" />

> **Takeaway:** On-time delivery is a necessary baseline to prevent churn, while repeat buying depends more on individual seller reliability, post-purchase support, and lifecycle engagement.

---

# 4. Recommendations:
| Horizon | Focus area | Concrete actions | Target impact |
| :--- | :--- | :--- | :--- |
| **Short-term**<br>*(30–60 days)* | **SLA and promise calibration** | Dynamically adjust estimated delivery dates by real seller-to-buyer distance; enforce tighter 3PL carrier SLA tracking. | Cut **3+ day delays** and protect >4.0★ ratings on cross-state orders. |
| **Medium-term**<br>*(1–3 months)* | **Trust and lifecycle nudges** | Tighten seller/product quality vetting; automate post-purchase reminders (maintenance, usage tips, add-ons) timed to product lifecycles. | Convert first-time durable-goods buyers into repeat customers. |
| **Long-term**<br>*(3–6 months)* | **Linehaul and hub optimization** | Reallocate cross-state volume to top-performing long-haul carriers and build local consolidation hubs for `NE` and `SE` corridors. | Narrow the cross-state vs. intra-state transit gap (14.1 vs. 7.0 days). |

# 5. Limitations:
- **Predictive retention modeling:** Extend descriptive segmentation with classification/regression models to quantify independent predictors of repeat purchase after the first order.
- **Seller-level granularity:** Drill down into individual seller handoff speeds and defect rates (beyond aggregate seller faults of **12.59%** and joint faults of **9.72%**) to build seller-tier SLA alerts.
- **Review NLP & sentiment analysis:** Apply text analysis on review comments to separate logistics delays from product quality, packaging, or description mismatches.

# Contact
**Tran Ngoc Anh Minh (Orla)** - minh.1503003@gmail.com

📂 **Repository:** `https://github.com/<orla-1503>/olist_customer_retention_analysis`



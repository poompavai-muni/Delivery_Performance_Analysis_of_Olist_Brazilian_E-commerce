# Olist Delivery Performance & Customer Satisfaction Analysis

An exploratory data analysis of delivery performance on the Olist Brazilian e-commerce marketplace. The project measures how long orders take to arrive, how often they arrive later than promised, when delays peaked, and whether late deliveries lead to lower customer review scores.

**Tools:** Python, pandas, NumPy, Matplotlib, Jupyter / Google Colab

---

## Headline Results

| Question 					| Answer 
|---						|---
| How long does a typical delivery take? 	| **10.2 days** (median), purchase to customer 
| How often is an order late? 			| **6.8%** of delivered orders (6,534 of 96,470) 
| Are the delivery estimates accurate? 		| Very conservative: Olist promised a median of **24 days**, but 							orders took **10.2 days** 
| When were deliveries worst? 			| **March 2018**: 18.96% of orders purchased that month arrived 							late 
| Does lateness hurt reviews? 			| Yes. Average score falls from **4.29** (early) to **1.67** (8-14 							days late) 

## Project Goals

1. Clean and validate the order, review and geolocation data.
2. Measure delivery time from purchase to arrival.
3. Compare actual delivery dates with the estimated delivery dates.
4. Find when deliveries ran late and how severe the delays were.
5. Test whether late deliveries are linked to lower review scores.

## Dataset

[Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) (Kaggle): **99,441 orders** placed between 2016 and 2018, spread over several related tables.

| File 					| Used for |
|---					|---|
| `olist_orders_dataset.csv` 		| Order status and timestamps |
| `olist_order_reviews_dataset.csv` 	| Review scores |
| `olist_customers_dataset.csv` 	| Loaded and checked |
| `olist_geolocation_dataset.csv` 	| Cleaned, one row per ZIP prefix, for future map analysis |
| `olist_order_items_dataset.csv`,
  `olist_order_payments_dataset.csv`, 
  `olist_products_dataset.csv`,
  `olist_sellers_dataset.csv`, 
  `product_category_name_translation.csv`| Loaded and checked, not yet analysed |

The data is not included in this repository. Download it from Kaggle (see *How to Run*).

## Methodology

**Data cleaning and validation**
- Checked every table for missing values and duplicates.
- **Geolocation:** the raw table (1,000,163 rows) is kept unchanged. A second table, `geo_group`, has one row per ZIP prefix (19,015 rows, median latitude/longitude). Joining the raw table to orders would repeat each order many times.
- **Reviews:** missing comments were kept empty (with a `has_comment` flag) instead of being filled with placeholder text. 547 orders had more than one review, so only the latest review per order was kept (98,673 reviews).
- **Orders:** timestamps were converted to datetime, and durations were calculated in exact days (including fractions of a day).
- Timestamp anomalies were measured and investigated, not deleted automatically.

**Metrics**
- `delivery_time`: purchase to customer delivery, in days.
- `days_late`: actual delivery date minus estimated delivery date (dates only). Orders are labelled Early, On time or Late.
- Monthly results are grouped by **purchase month**, because grouping by delivery month would push slow orders into later months and distort the trend. Months with fewer than 100 orders are not plotted.
- Monthly delivery time uses the **median**, because a few very slow orders pull the average up.

## Key Findings

### 1. Delivery time
- Analysis covers **96,470 delivered orders**: 97.0% of all 99,441 orders.
- Delivery time (purchase to customer): **median 10.2 days**, mean 12.6 days, Q1 6.8 days, Q3 15.7 days, maximum 209.6 days. The mean is higher than the median because of a small group of very slow orders.

| Delivery time 	| Orders 		| Share	 |
|---			|---			|---	 |
| Up to 7 days 		| 26,046 		| 27.0%  |
| 7-14 days 		| 40,212 		| 41.7%  |
| 14-30 days 		| 25,662 		| 26.6%  |
| 30-60 days 		| 4,244 		| 4.4%   |
| Over 60 days 		| 306 			| 0.3%   |

**68.7%** of orders arrived within 14 days, and **4.7%** (4,550 orders) took more than 30 days.


### 2. Delivery vs the estimated date
- **88,644 orders (91.9%)** arrived early, **1,292 (1.3%)** on time and **6,534 (6.8%)** late.
- On average, orders arrived **11.9 days before** the estimated date. The longest delay was **188 days**.
- The high "early" share mostly shows that the estimates are padded: the median promise was **24 days**, while the median actual delivery was **10.2 days**. The **6.8% late rate** is the fairer measure of performance.


### 3. Monthly trend (by purchase month)

| Period 	| Late rate 			| Note 						|
|---		|---				|---						|
| Jan-Oct 2017 	| 2.8% to 6.6% 			| Stable. Highest was April 2017 (6.56%) 	|
| **Nov 2017** 	| **12.40%** (904 of 7,288) 	| Highest order volume of any month 		|
| Dec 2017 - 
     Jan 2018 	| 7.46% and 5.70% 		| Partial recovery 				|
| **Feb 2018** 	| **14.13%** (926 of 6,555) 	| Median delivery time peaked at 14.3 days 	|
| **Mar 2018** 	| **18.96%** (1,328 of 7,003) 	| Worst month in the data 			|
| Apr 2018 	| 4.50% 			| Sharp improvement 				|
| Jun 2018 	| 1.16% 			| Best month 					|
| Aug 2018 	| 6.19% 			| Median delivery time 7.0 days, the lowest 	|

The jump in late deliveries followed the record order volume in November 2017, which may point to a capacity problem. The data alone cannot prove the cause.


### 4. Delivery lateness and review scores
After joining one review per order to the delivery data:

| Delivery vs estimate  | Reviews| Average score |
|---		        |---	 |---		 |
| Early 		| 88,163 | 4.29		 |
| On time 		| 1,280  | 4.03		 |
| 1-3 days late 	| 1,852  | 3.29		 |
| 4-7 days late 	| 1,748  | 2.10		 |
| 8-14 days late 	| 1,446  | 1.67		 |
| 15+ days late 	| 1,335  | 1.72		 |

- A delay of just 1-3 days lowers the average score by one point compared with an early delivery (4.29 vs 3.29), and the score is **2.62 points lower** (4.29 vs 1.67) when the delay reaches 8-14 days.
- After about 8 days of delay the score stops falling (1.67 vs 1.72). Customers are already very unhappy by then.
- Overall, 57.8% of all reviews are 5 stars and 11.5% are 1 star.

### 5. Data-quality findings
- **1,359 orders (1.4%)** show the carrier pickup *before* the payment approval. The median gap is about 17 hours. **93.8%** of them are in 2018, mostly July (565) and April (393). They are flagged as anomalies, not deleted, because a seller can ship before a payment is approved.
- **23 orders** show the customer receiving the parcel before the carrier pickup date, which is logically inconsistent.
- **56 orders** have a carrier-to-customer time above 100 days (mean 146 days, maximum 205 days).
- **28 orders** that took over 60 days were all delivered on **19 September 2017**, although they were placed in different months. This looks like a system or batch recording event, so these records may not reflect real delays.
- No order was delivered before it was purchased.

## Limitations

- Only orders with the status *delivered* are measured. Canceled, unavailable and undelivered orders are left out, so the true late rate is probably higher than 6.8%.
- The review analysis shows an association, not proof of cause. Other factors, such as product quality, also affect scores.
- Only customers who left a review are counted in section 4.
- Geolocation data is at ZIP-prefix level. It does not give exact addresses.
- The final purchase months (such as August 2018) may look better than they really are, because very slow orders had not yet been delivered when the data ended.

## How to Run

1. Download the dataset from [Kaggle](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) and unzip it.
2. Install the libraries:
   ```bash
   pip install pandas numpy matplotlib jupyter
   ```
3. Open `olist_delivery_performance_analysis.ipynb`.
4. In the loading cell, change the paths from `/content/...` (Google Colab) to the folder with your CSV files.
5. Run the cells from top to bottom.

## Repository Structure

```
├── olist_delivery_performance_analysis.ipynb   # main analysis
├── images/                                     # charts used in this README
└── README.md
```

## Next Steps

- Join order items, products and sellers to find which categories or sellers cause the most delays.
- Calculate the distance between seller and customer and compare it with delivery time.
- Map the late rate by customer state.
- Build a simple model to predict whether an order will be late.

## Acknowledgements

Data provided by Olist and published on Kaggle under the [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/) license.

## Author

**POOMPAVAI V**
GitHub Link: https://github.com/poompavai-muni/Delivery_Performance_Analysis_of_Olist_Brazilian_E-commerce.git

# Customer Retention & Segmentation Analysis: Online Retail Dataset

## 📖 Overview
This project explores a transactional dataset from an online retail store to uncover customer purchasing behaviors, track cohort retention over time, and segment users based on their purchase frequency. The analysis moves from initial data cleaning and anomaly detection (such as identifying left-censoring bias) to actionable RFM (Recency, Frequency, Monetary) segmentation.

## 🛠️ 1. Data Exploration & Cleaning
The initial dataset consisted of 541,909 rows and 8 columns (InvoiceNo, StockCode, Description, Quantity, InvoiceDate, UnitPrice, CustomerID, Country).

During the initial exploration, I identified significant missing data, including 1,454 missing Description values and 135,080 missing CustomerID values. To prepare the data for accurate cohort and user-level analysis, I performed the following cleaning steps:
- Dropped Missing IDs: Removed all rows lacking a CustomerID, as anonymous transactions cannot be tracked for retention.
- Removed Cancellations: Filtered out cancelled orders, which are characterized by an invoice number starting with 'C'.
- Removed Invalid Data: Kept only transactions with positive Quantity and UnitPrice values to eliminate invalid entries and manual adjustments.

**Result:** A clean, analysis-ready dataset containing 397,884 rows.

## ⚙️ 2. Feature Engineering
To enable time-series and monetary analysis, I engineered the following features:
- Revenue: Created a new column by multiplying Quantity by UnitPrice.
- Time Formatting: Converted InvoiceDate to a proper standard datetime format.
- Invoice Month: Extracted the month and year from the invoice date to serve as the foundation for the cohort matrix.

## 📊 3. Cohort Retention Analysis
I built a cohort retention matrix to track how different groups of customers (grouped by their first purchase month) remained active over time.

**Methodology**

To construct the retention matrix, each customer was assigned to a specific cohort based on the month of their very first purchase. Every subsequent transaction was then evaluated to calculate a "Cohort Index", representing the number of months elapsed since that initial acquisition. The dataset was aggregated to count the unique number of returning customers per cohort for each active period. This aggregated data was pivoted into a grid format, converted into retention percentages relative to the initial size of each cohort, and visualized as a heatmap to easily identify behavioral trends.

**Key Observations & Bias Detection**

Upon visualizing the matrix, I observed several distinct patterns that required deeper investigation:
  1. The "Super Cohort" Anomaly: The December 2010 cohort retained at an unusually high 38% by Period 3 (the best Period 3 performance across all cohorts) and consistently outperformed other months across the board.
  2. Seasonal Peaks: Regardless of the cohort's age, periods corresponding to the calendar month of November showed the highest retention spikes.
  3. The December 2011 Drop-off: Periods corresponding to December 2011 showed the lowest retention values globally.

**Diagnosing the Data Constraints:**

- **Left-Censoring Bias:** I hypothesized that the inflated December 2010 figures were due to left-censoring (legacy customers being flagged as "new" simply because the dataset's tracking started that month). I confirmed this hypothesis by creating a test retention matrix starting in January 2011, which replicated the exact same artificial spike for the new first month.
- **Incomplete Data:** The steep drop-off in December 2011 is an artifact of the dataset terminating on December 9th, capturing only 9 days of activity.

## 📈 Key Metrics & Business Recommendations
To ensure accurate reporting, I deliberately excluded the left-censored December 2010 cohort when extracting aggregate lifecycle metrics. This revealed the true behavioral trends of new customer acquisitions:
- Average Retention at Month 1: 19.2%
- Average Retention at Month 6: 22.4%
- Best Cohort at Month 3: February 2011 (28.4%)

**Business Insights:**
1. Cyclical Purchasing Behavior: In standard B2C retail, retention usually resembles a steep slide, continually decaying after Month 1. However, this data does the exact opposite: Month 6 retention (22.4%) is higher than Month 1 retention (19.2%). This confirms a strong presence of B2B wholesale buyers who purchase in bulk, deplete inventory, and return roughly twice a year for massive restocks.
   - **Recommendation:** Marketing should minimize aggressive short-term remarketing ad spend immediately after a first purchase. Instead, operations should deploy automated bulk-restock reminders and targeted account outreach exactly 90 to 150 days post-purchase.
2. **The Intent-Driven "Gold Standard" Cohort:** Customers acquired in February 2011 achieved a massive 28.4% retention rate by Month 3 (May). In UK retail, May marks the preparation window for summer catalogs.
   - **Recommendation:** The marketing team should audit the acquisition channels and ad creatives deployed in February 2011. Replicating that specific spend allocation in Q1 is highly likely to capture intent-driven seasonal buyers securing their summer suppliers.

## 🎯 4. Customer Segmentation & Revenue Impact
Following the temporal cohort analysis, I pivoted to a behavioral segmentation model to categorize customers based on their total purchase frequency.

**Methodology**

To understand the distribution of customer value, the dataset was aggregated at the customer level to calculate two key metrics: order frequency (total unique invoices) and monetary value (total revenue generated). Customers were then categorized into three distinct operational segments:

- One-Time: 1 Order
- Repeat: 2 to 4 Orders
- VIP: 5+ Orders

Once categorized, I aggregated the total number of customers and the total revenue generated within each segment. These raw figures were converted into percentages to highlight the proportional contribution of each group and visualized using side-by-side bar charts.

Segmentation Results

|Segment|% of Customer Base|% of Total RevenueOne|
|---|---|---|
|One Time|34.4%|6.9%|
|Repeat|39.9%|21.9%|
|VIP|25.7%|71.2%|

**Final Business Insight**

The segmentation reveals a textbook operational imbalance (Pareto Principle): ***VIP customers, while only making up 25.7% of the total customer base, are driving a massive 71.2% of the total revenue.*** Meanwhile, over a third of the customer base (34.4%) are one-time buyers who contribute less than 7% to the bottom line. This indicates that business resources should aggressively pivot toward white-glove VIP account management and incentivizing the crucial second purchase, rather than focusing purely on top-of-funnel acquisition.


# Customer Analytics and RFM Segmentation

## 📌 Project Overview

This project focuses on customer analytics and RFM (Recency, Frequency, Monetary) segmentation to identify the most valuable customers and customers at risk of churning.

Using SQL Server, Python, and Power BI, transactional data from the Online Retail II dataset was cleaned, analyzed, and transformed into actionable customer segments.

The final solution provides business stakeholders with insights into customer value, purchasing behavior, revenue contribution, and retention performance, helping them make data-driven decisions and develop targeted customer retention strategies.

---

## 🎯 Business Problem

The business wants to identify its most valuable customers and reduce churn among customers who are at risk of leaving.

Leadership currently has no structured view of customer value, making retention efforts reactive rather than targeted.

This project addresses the problem by implementing RFM-based customer segmentation and developing an interactive Power BI dashboard to monitor customer performance and retention.

## ❓ Business Questions Answered

- Who are our most valuable customers, and how much revenue do they actually contribute?
- Which customers have gone quiet and are at risk of churning?
- How does customer value and retention trend across time, by month, quarter, and year?
- How does customer value and churn differ across geographic markets?
- What specific actions should the business take for each customer segment?

---

## 📊 Key Findings

- **Revenue Concentration:** Champions represent approximately 22% of the customer base but generate close to 68% of total revenue, demonstrating a strong concentration of revenue among high-value customers.
- **Customer Churn:** Overall churn stands at 50.86%, using a 90-day inactivity threshold, indicating a significant customer retention challenge.
- **Customer Value:** Average customer value varies considerably across segments, ranging from a few hundred for Lost customers to several thousand for Champions.
- **Seasonality:** Revenue shows noticeable seasonal spikes in Q4, consistent with the gifting-focused nature of the business.

---

## 📂 Data Source

**Dataset:** Online Retail II

**Source:** UCI Machine Learning Repository

**Donated by:** Daqing Chen

The dataset contains real, anonymized transactional records from a UK-based, non-store online retailer selling giftware for various occasions.

| Dataset Attribute | Details |
|---|---|
| Time Period | December 2009 – December 2011 |
| Raw Transaction Rows | 1,067,371 |
| Dataset Structure | Two Excel sheets |
| Business Type | Online Retail |
| License | Creative Commons Attribution 4.0 International |

Dataset Link: [Online Retail II - UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/502/online+retail+ii)

---

## 🛠️ Tools & Technologies

| Tool | Purpose |
|---|---|
| SQL Server / SSMS | Data import, cleaning, deduplication, and exploratory analysis on the full 1M+ row dataset |
| Python (Pandas) | RFM calculation, customer scoring, segmentation logic, and churn flagging |
| Jupyter Notebook | Interactive development environment for Python analysis |
| Power BI Desktop | Development of a three-page interactive dashboard |
| DAX | Measures for customer counts, revenue totals, churn percentage, and average customer value |

---

## 🧠 Core Concepts Demonstrated

### 1. RFM Analysis
Evaluated customers based on three key dimensions:

- **Recency:** How recently a customer made a purchase.
- **Frequency:** How frequently a customer makes purchases.
- **Monetary:** How much revenue a customer generates.

Each customer was assigned a score from 1 to 5 for each dimension to quantify customer value.

### 2. Customer Segmentation
Translated RFM scores into six meaningful customer segments:

- Champions
- Loyal
- At Risk
- New
- Needs Attention
- Lost

This segmentation helps the business understand different customer behaviors and develop targeted engagement strategies.

### 3. Churn Definition and Flagging
Applied a fixed 90-day inactivity threshold to identify customers who have not made a purchase recently.

This provides a structured approach to monitoring customer retention and identifying potential churn risks.

### 4. Data Quality Handling
- Resolved mixed date formats.
- Handled invalid data type conversions.
- Identified and removed duplicate records.
- Used SQL window functions for duplicate detection.
- Validated row counts against the published UCI dataset total.

### 5. Dimensional Modeling for BI
Structured the segmentation results and transaction-level data into a unified analysis-ready table, allowing multiple dashboard pages to use a consistent data source.

### 6. Interactive BI Design
Implemented:
- Interactive slicers.
- Bookmark-based toggles.
- Multi-page navigation.
- Dynamic KPI cards.
- Time-based analysis.
- Customer segment comparisons.

---

## ⚙️ Project Workflow

### Step 1 — Business Requirements Document

Defined the project scope, business questions, key metric definitions, and success criteria before starting the data analysis.

### Step 2 — SQL: Data Cleaning and Exploration

- Imported both raw Excel sheets into SQL Server.
- Corrected data types and handled invalid values.
- Resolved a mixed date format issue affecting more than half of the dataset.
- Removed 32,907 exact duplicate rows.
- Validated row counts at each stage against the published UCI dataset total.

### Step 3 — Python: RFM Segmentation

- Loaded the cleaned transactional data into Pandas.
- Calculated Recency, Frequency, and Monetary values for each customer.
- Assigned scores from 1 to 5 for each RFM dimension.
- Segmented 5,881 customers into six distinct customer groups.
- Created a 90-day churn flag to identify inactive customers.

### Step 4 — Power BI: Dashboard Development

- Merged customer segmentation results with transaction-level data.
- Created a unified analysis-ready dataset.
- Developed a three-page interactive Power BI dashboard.
- Implemented DAX measures for customer metrics, revenue, and churn analysis.
- Added slicers, bookmark-based toggles, and navigation features.

---

## 📈 Dashboard Structure

The Power BI report consists of three interactive pages.

### Page 1 — Home

Provides an introduction to the project and navigation to the analytical report pages.

Features:
- Project title and overview.
- Navigation buttons to report pages.
- Glossary explaining Recency, Frequency, Monetary, and Churned %.

### Page 2 — Business Overview

Provides an executive-level overview of customer performance and business health.

**Key Performance Indicators:**
- Total Customers
- Total Revenue
- Churn %
- Average Customer Value

**Visualizations and Analysis:**
- Revenue and customer analysis toggle.
- Customer segment breakdown.
- Monthly revenue and customer trends.
- Quarterly performance analysis.
- Yearly performance analysis.

**Interactive Filters:**
- Month
- Quarter
- Year
- Country
- Customer Segment
- Churn Status

### Page 3 — RFM Analysis

Provides a detailed view of customer segmentation and segment-level performance.

**Key Analysis:**
- Customer count by segment.
- Revenue contribution by segment.
- Average Recency, Frequency, and Monetary scores by segment.
- Average customer value across segments.
- Churn percentage trends over time.

This page helps identify high-value customer groups and segments that require targeted retention strategies.

---

## 💻 Sample SQL: Handling Mixed Date Formats

The following SQL logic handles multiple date formats using `TRY_CONVERT()` and `COALESCE()`.

```sql
SELECT
    Invoice,
    StockCode,
    Description,
    Quantity,
    COALESCE(
        TRY_CONVERT(datetime2, InvoiceDate, 120),
        TRY_CONVERT(datetime2, InvoiceDate, 105)
    ) AS InvoiceDate,
    TRY_CONVERT(decimal(10,2), Price) AS Price,
    Customer_ID,
    Country
INTO dbo.RetailCombined
FROM dbo.[Year 2009-2010];
```

## 🐍 Sample Python: RFM Scoring

The following code assigns customer scores based on their Recency, Frequency, and Monetary values.

```python
# Recency scoring (lower recency is better)
rfm['R_Score'] = pd.qcut(
    rfm['Recency'],
    5,
    labels=[5, 4, 3, 2, 1]
).astype(int)

# Frequency scoring (higher frequency is better)
rfm['F_Score'] = pd.qcut(
    rfm['Frequency'].rank(method='first'),
    5,
    labels=[1, 2, 3, 4, 5]
).astype(int)

# Monetary scoring (higher monetary value is better)
rfm['M_Score'] = pd.qcut(
    rfm['Monetary'],
    5,
    labels=[1, 2, 3, 4, 5]
).astype(int)
```

---

## 💡 Business Recommendations

Based on the customer segmentation analysis, the following strategies can help improve customer retention and revenue generation.

| Customer Segment | Recommended Business Action |
|---|---|
| Champions | Reward with exclusive offers, loyalty benefits, and early access to new products. |
| Loyal | Encourage repeat purchases through loyalty programs and personalized recommendations. |
| At Risk | Launch targeted re-engagement campaigns and personalized discounts. |
| New | Build engagement through welcome offers and follow-up campaigns. |
| Needs Attention | Encourage repeat purchases through personalized product recommendations. |
| Lost | Run win-back campaigns with special incentives to reactivate inactive customers. |

---

## 🚀 How to Reproduce This Project

Follow these steps to recreate the complete analysis:

1. Download the **Online Retail II** dataset from the [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/502/online+retail+ii).
2. Run the SQL scripts available in the `01_sql` folder in the specified order to recreate the cleaned `RetailCleaned` table.
3. Export the cleaned dataset from SQL Server to CSV.
4. Open the Jupyter Notebook in the `02_python` folder and execute the RFM analysis and customer segmentation workflow.
5. Generate the segmented customer dataset.
6. Open the Power BI file in the `03_powerbi` folder.
7. Update the data source path to the exported Excel file and refresh the dashboard.

---



## 👨‍💻 Skills Demonstrated

- SQL Querying and Data Cleaning
- SQL Window Functions
- Python Data Analysis
- Pandas and Data Manipulation
- RFM Analysis and Customer Segmentation
- Customer Churn Analysis
- Power BI Dashboard Development
- DAX Measures
- Data Modeling
- Business Intelligence
- Data-Driven Decision Making

---

## 📌 Project Information

**Project Name:** IKEA Gifting: Customer Analytics and RFM Segmentation

**Domain:** Retail Analytics / Customer Analytics

**Tools:** SQL Server, Python, Pandas, Jupyter Notebook, Power BI, DAX

**Dataset:** Online Retail II – UCI Machine Learning Repository

**Focus Areas:** Customer Segmentation | RFM Analysis | Churn Analysis | Revenue Analysis | Business Intelligence

---

⭐ If you find this project useful, feel free to star the repository!

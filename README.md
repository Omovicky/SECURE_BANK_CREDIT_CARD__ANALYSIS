# SecureBank Nigeria: Credit Card Risk, Fraud & Performance Analysis

![Status](https://img.shields.io/badge/Status-Completed-green)
![Tools](https://img.shields.io/badge/Tools-Excel%20%7C%20SQL%20Server%20%7C%20Power%20BI-blue)

## Project Overview
SecureBank Nigeria identified unusual patterns in credit card transactions and commissioned a full analytical review of 8,000 transactions to better understand spending behaviours, detect fraud patterns, and surface actionable insights.Te goal is to strengthen fraud detection protocol,improve customer service by understanding normal transaction behaviours, and provide data driven recommendation for to support the Chief Risk Officer in reducing risk and enhancing operational efficency.

## Objectives
- Analyse transaction behaviour and spending patterns across merchants, cities, card types, and cardholders
- Identify fraud trends, risk exposure, and anomalies across devices, IP addresses, and transaction sources
- Evaluate transaction performance including response codes and declined transactions
- Provide strategic recommendations to reduce fraud and improve operational efficiency

  ## Tools and Technologies
| Tool | Purpose |
|---|---|
| Microsoft Excel | Data cleaning and transformation |
| SQL Server SSMS | Data import, querying, and answering 15 business questions |
| Power BI Desktop | Interactive dashboard development and visualisation |
| PowerPoint | Presentation of findings to stakeholder audience |

## Dataset overview
| Property | Detail |
|----------|--------|
| Source | Kaggle — teamincribo/credit-card-fraud |
| File | credit_card_fraud.csv |
| Rows | 8,000 transactions |
| Columns | 20 original — 23 after cleaning |
| Date Range | January 2020 — December 2023 |
| Currencies | INR, USD, EUR |
Dataset Note: The dataset contains Indian city names and INR currency despite being framed as a Nigerian bank project. INR transactions were treated as local and USD/EUR as international for analytical purposes. This limitation is documented transparently throughout the analysis.

### Column Reference

| Column | Data Type | Description |
|---|---|---|
| Transaction_Datetime | datetime | Full transaction timestamp |
| DATE | date | Extracted transaction date |
| HOUR | number | Extracted transaction hour 0 to 23 |
| DAY_OF_THE_WEEK | text | Day name e.g. Monday |
| MONTH | text | Month name e.g. January |
| Amount | money | Transaction amount |
| Cardholder_name | text | Full name of cardholder |
| Merchant_name | text | Merchant business name |
| MCC | number | Merchant Category Code |
| City | text | Transaction city |
| Currency | text | INR, USD, or EUR |
| Card_type | text | Visa, Mastercard, American Express |
| Response_code | number | 0 Approved, 5 Declined, 12 Failed |
| Transaction_id | text | Unique transaction identifier |
| fraud_label | text | Fraudlenr or legitimate |
| Prev_transactions | text | 1, 2, 3 or more, Unknown |
| Tran_Source | text | Online or In-Person |
| IP_address | text | Transaction IP address |
| Device_type | text | Mobile, Desktop, or Tablet |

### Dataset Limitations

| Limitation | Impact |
|---|---|
| Indian geography | Dataset contains Indian cities despite Nigerian bank framing — INR treated as local, USD and EUR as international |
| No ZIP code | Geographic analysis limited to city level only |
| No cardholder ID | Cardholder name used as unique identifier |
| MCC numeric only | No category name mapping available |
| fraud_label typo | Stored as Fraudlenr instead of Fraudulent due to Excel formula error — all SQL queries reference stored value |

---

## Data Cleaning Process

### Issues Identified

| Number | Column | Issue | Records | Action |
|---|---|---|---|---|
| 1 | Previous Transactions | 2,043 missing values | 2,043 | Filled with Unknown |
| 2 | User Account Information | 4,010 missing values | 4,010 | Filled with Unknown |
| 3 | Transaction Notes | Random junk placeholder text | 8,000 | Column deleted |
| 4 | All columns | Long inconsistent names | 20 cols | All renamed |
| 5 | Transaction Date and Time | Stored as text | 8,000 | Converted and split |
| 6 | Multiple columns | Leading trailing spaces in headers | 6 cols | Spaces removed |
| 7 | Previous Transactions | None and Unknown used inconsistently | Multiple | Replaced None with Unknown |
| 8 | Transaction ID | Checked for duplicates | 0 found | No action needed |
| 9 | Transaction Amount | Checked for negative values | 0 found | No action needed |
### Cleaning Steps

Step 1 — Opened raw CSV in Excel and saved immediately as securebank_cleaned.xlsx to protect the original file

Step 2 — Converted data to Excel Table using Ctrl + T and froze header row

Step 3 — Filled 2,043 blank cells in Previous Transactions with Unknown using Ctrl + G — Special — Blanks

Step 4 — Filled 4,010 blank cells in User Account Information with Unknown using same method

Step 5 — Used Ctrl + H Find and Replace to replace all None values with Unknown in Previous Transactions column

Step 6 — Deleted the Transaction Notes column as it contained random placeholder text with no analytical value

Step 7 — Added 4 helper columns for time analysis:
- DATE using formula =INT(A2) formatted as Date
- HOUR using formula =HOUR(A2) formatted as Number
- DAY OF THE WEEK using formula =TEXT(A2,"DDDD")
- MONTH using formula =TEXT(A2,"MMMM")

Step 8 — Formatted Transaction Amount column as Number with 2 decimal places

Step 9 — Removed leading and trailing spaces from 6 column headers

Step 10 — Renamed all 23 columns to short SQL-friendly names

Step 11 — Added fraud_label column with formula =IF(R2=1,"Fraudlenr","legitimate")

Step 12 — Saved final cleaned file as credit_card_fraud_clean.xlsx

## Exploratory Analysis

### Database Setup

```sql
-- Create database
CREATE DATABASE Credit_card_fraud

-- Verify import was successful
SELECT TOP 10 *
FROM [dbo].[credit_card_fraud clean]

-- Confirm row count
SELECT COUNT(*) AS total_rows
FROM [dbo].[credit_card_fraud clean]
-- VERIFY IMPORT
SELECT TOP 10 *
FROM [dbo].[credit_card_fraud clean]

SELECT COUNT(*) AS total_rows
FROM [dbo].[credit_card_fraud clean]

-- Q1: TOTAL TRANSACTION AMOUNTS AND COUNTS OVER TIME
SELECT
    Date,
    MONTH,
    COUNT(*) AS total_transactions,
    SUM([Amount]) AS total_amount,
    AVG([Amount]) AS avg_amount
FROM [dbo].[credit_card_fraud clean]
GROUP BY DATE, MONTH
ORDER BY DATE

-- Q2: MERCHANTS GENERATING HIGHEST TRANSACTION REVENUE
SELECT TOP 20
    Merchant_name,
    COUNT(*) AS total_transaction,
    SUM([Amount]) AS total_revenue,
    AVG([Amount]) AS transaction_value,
    SUM(CASE WHEN is_label = 1 THEN 1 ELSE 0 END) AS fraud_count
FROM [dbo].[credit_card_fraud clean]
GROUP BY Merchant_name
ORDER BY total_revenue DESC

-- Q3: MOST COMMON MERCHANT CATEGORIES MCC BY VOLUME
SELECT TOP 15
    MCC,
    COUNT(*) AS total_transaction,
    SUM([Amount]) AS total_amount
FROM [dbo].[credit_card_fraud clean]
GROUP BY MCC
ORDER BY total_transaction DESC

-- Q4: CITIES WITH HIGHEST TRANSACTION ACTIVITY
SELECT TOP 20
    [City],
    COUNT(*) AS transaction_count,
    SUM([Amount]) AS total_amount
FROM [dbo].[credit_card_fraud clean]
GROUP BY [City]
ORDER BY transaction_count DESC

-- Q5: PEAK TRANSACTION HOURS AND DAYS OF THE WEEK
SELECT
    HOUR,
    DATE,
    DAY_OF_THE_WEEK,
    COUNT(*) AS transaction_count,
    SUM([Amount]) AS total_amount
FROM [dbo].[credit_card_fraud clean]
GROUP BY HOUR, date, DAY_OF_THE_WEEK
ORDER BY transaction_count DESC

-- Q6: CARD TYPES WITH HIGHEST TRANSACTION VOLUMES
SELECT
    Card_type,
    COUNT(*) AS transaction_count,
    SUM([Amount]) AS total_amount,
    AVG([Amount]) AS avg_amount
FROM [dbo].[credit_card_fraud clean]
GROUP BY card_type
ORDER BY transaction_count DESC

-- Q7: CARDHOLDERS WITH HIGHEST TOTAL SPENDING
SELECT TOP 10
    Cardholder_name,
    COUNT(*) AS transaction_count,
    SUM([Amount]) AS total_spending,
    AVG([Amount]) AS avg_transaction,
    MAX([Amount]) AS max_single_tnx
FROM [dbo].[credit_card_fraud clean]
GROUP BY Cardholder_name
ORDER BY total_spending DESC

-- Q8: PERCENTAGE OF TRANSACTIONS FLAGGED AS FRAUDULENT
SELECT
    COUNT(*) AS total_transaction,
    SUM(CASE WHEN [fraud_label] = 'Fraudlenr'
        THEN 1 ELSE 0 END) AS fraud_count,
    SUM(CASE WHEN [fraud_label] = 'legitimate'
        THEN 1 ELSE 0 END) AS legitimate_count,
    CAST(ROUND(
        SUM(CASE WHEN [fraud_label] = 'Fraudlenr'
            THEN 1.0 ELSE 0 END)
        / COUNT(*) * 100, 2)
    AS DECIMAL(10,2)) AS fraud_percentage,
    CAST(ROUND(
        SUM(CASE WHEN [fraud_label] = 'legitimate'
            THEN 1.0 ELSE 0 END)
        / COUNT(*) * 100, 2)
    AS DECIMAL(10,2)) AS legitimate_percentage
FROM [dbo].[credit_card_fraud clean]

-- Q9A: FRAUD BY DEVICE TYPE
SELECT
    Device_type,
    COUNT(*) AS total_transactions,
    SUM(CASE WHEN [fraud_label] = 'fraudlenr'
        THEN 1 ELSE 0 END) AS fraud_count,
    CAST(ROUND(
        SUM(CASE WHEN [fraud_label] = 'fraudlenr'
            THEN 1.0 ELSE 0 END)
        / COUNT(*) * 100, 2)
    AS DECIMAL(10,2)) AS fraud_rate_pct
FROM [dbo].[credit_card_fraud clean]
GROUP BY device_type
ORDER BY fraud_rate_pct DESC

-- Q9B: TOP IP ADDRESSES WITH MOST FRAUDULENT TRANSACTIONS
SELECT TOP 20
    IP_address,
    COUNT(*) AS total_transactions,
    SUM(CASE WHEN [fraud_label] = 'fraudlenr'
        THEN 1 ELSE 0 END) AS fraud_count
FROM [dbo].[credit_card_fraud clean]
WHERE [fraud_label] = 'fraudlenr'
GROUP BY IP_address
ORDER BY fraud_count DESC

-- Q10: TRANSACTION AMOUNTS BY TRANSACTION SOURCE
SELECT
    Tran_Source,
    COUNT(*) AS transaction_count,
    CAST(ROUND(SUM([Amount]), 2)
        AS DECIMAL(10,2)) AS total_amount,
    CAST(ROUND(AVG([Amount]), 2)
        AS DECIMAL(10,2)) AS AVG_amount,
    SUM(CASE WHEN [fraud_label] = 'fraudlenr'
        THEN 1 ELSE 0 END) AS fraud_count,
    CAST(ROUND(
        SUM(CASE WHEN [fraud_label] = 'fraudlenr'
            THEN 1.0 ELSE 0 END)
        / COUNT(*) * 100, 2)
    AS DECIMAL(10,2)) AS fraud_rate_pct
FROM [dbo].[credit_card_fraud clean]
GROUP BY tran_source
ORDER BY total_amount DESC

-- Q11: MOST FREQUENT RESPONSE CODES
SELECT
    [Response_code],
    COUNT(*) AS occurrence_count,
    CAST(ROUND(
        COUNT(*) * 100.0 / SUM(COUNT(*)) OVER(), 2)
    AS DECIMAL(10,2)) AS pct_of_total,
    SUM(CASE WHEN fraud_label = 'Fraudlenr'
        THEN 1 ELSE 0 END) AS fraud_count
FROM [dbo].[credit_card_fraud clean]
GROUP BY [Response_code]
ORDER BY occurrence_count DESC

-- Q12: DUPLICATE OR REPEATED TRANSACTION IDs
SELECT
    COUNT(*) AS total_records,
    COUNT(DISTINCT Transaction_id) AS unique_txn_ids,
    COUNT(*) - COUNT(DISTINCT Transaction_id) AS duplicate_count
FROM [dbo].[credit_card_fraud clean]

-- Q13: INTERNATIONAL VS LOCAL TRANSACTIONS
SELECT
    Currency,
    CASE
        WHEN Currency = 'INR' THEN 'Local'
        ELSE 'International'
    END AS transaction_type,
    COUNT(*) AS transaction_count,
    CAST(ROUND(SUM([Amount]), 2)
        AS DECIMAL(10,2)) AS total_amount,
    CAST(ROUND(AVG([Amount]), 2)
        AS DECIMAL(10,2)) AS avg_amount,
    SUM(CASE WHEN fraud_label = 'Fraudlenr'
        THEN 1 ELSE 0 END) AS fraud_count,
    CAST(ROUND(
        SUM(CASE WHEN fraud_label = 'Fraudlenr'
            THEN 1.0 ELSE 0 END)
        / COUNT(*) * 100, 2)
    AS DECIMAL(10,2)) AS fraud_rate_pct
FROM [dbo].[credit_card_fraud clean]
GROUP BY Currency,
    CASE
        WHEN Currency = 'INR' THEN 'Local'
        ELSE 'International'
    END
ORDER BY transaction_count DESC

-- Q14: HIGH VALUE TRANSACTIONS RELATIVE TO CARDHOLDER HISTORY
WITH cardholder_stats AS (
    SELECT
        Cardholder_name,
        AVG([Amount]) AS avg_spend,
        STDEV([Amount]) AS std_spend
    FROM [dbo].[credit_card_fraud clean]
    GROUP BY Cardholder_name
),
flagged AS (
    SELECT
        t.Transaction_id,
        t.Cardholder_name,
        t.[Amount] AS transaction_amount,
        t.Transaction_Datetime,
        t.Merchant_name,
        t.fraud_label,
        cs.avg_spend,
        CAST(ROUND(
            (t.[Amount] - cs.avg_spend)
            / NULLIF(cs.std_spend, 0), 2)
        AS DECIMAL(10,2)) AS z_score
    FROM [dbo].[credit_card_fraud clean] t
    JOIN cardholder_stats cs
        ON t.Cardholder_name = cs.Cardholder_name
)
SELECT TOP 50 *
FROM flagged
WHERE z_score > 2
ORDER BY z_score DESC

-- Q15: CORRELATION BETWEEN MERCHANT CATEGORY LOCATION AND FRAUD
SELECT TOP 20
    [MCC] AS merchant_category,
    [City] AS city,
    COUNT(*) AS total_transactions,
    SUM(CASE WHEN fraud_label = 'Fraudlenr'
        THEN 1 ELSE 0 END) AS fraud_count,
    CAST(ROUND(
        SUM(CASE WHEN fraud_label = 'Fraudlenr'
            THEN 1.0 ELSE 0 END)
        / COUNT(*) * 100, 2)
    AS DECIMAL(10,2)) AS fraud_rate_pct,
    CAST(ROUND(SUM([Amount]), 2)
        AS DECIMAL(10,2)) AS total_amount
FROM [dbo].[credit_card_fraud clean]
GROUP BY [MCC], [City]
HAVING COUNT(*) >= 2
ORDER BY fraud_count DESC

-- END OF ALL 15 QUERIES
```

## Power BI Dashboard
### Executive Summary
KPI Cards:
Transaction Count:        8,000
Fraud Percentage:         49.86%
INR Volume:               6.72M
EUR Volume:               6.60M
USD Volume:               6.65M
Declined Transactions:    2,648

Visuals:
- Transaction Amount and Count Over Time (Line and Bar)
-📱 Fraudulent Transactions by Device Type (Bar chart)
- 🏪Top Merchants by Transaction Volume (Horizontal bar)
-⏰ Transaction Volume by Hour (Column chart)

<img width="720" height="469" alt="Executive Summary Dashbaord" src="https://github.com/user-attachments/assets/0b33647c-dae4-4473-afd7-7d72ae8eadaf" />

### Transaction behaviour and Spending Analysis
KPI Cards:
Unique Card Holders:      7,651
Unique Merchants:         7,027
Peak Transaction Day:     Wednesday
Peak Transaction Hour:    10:00 AM

Visuals:
- ⏰Transaction Volume by Hour (Column chart)
- Transaction Volume by Day (Column chart)
- 🌐Total Transaction Count by City (Horizontal bar)
- 🛒Card Type by Transaction Volume (Clustered bar)
- Top Cardholders by Total Spending (Horizontal bar)

<img width="720" height="446" alt="Transcation behaviour dashboard" src="https://github.com/user-attachments/assets/91b9cbd7-ba55-44c0-b658-cfa5a20652dd" />

### Fraud Detection Anomalies and Risk Insights
KPI Cards:
Fraud Percentage:         49.86%
Local Fraud Rate:         49.50%
International Fraud Rate: 50.05%
Declined Transactions:    2,648

Visuals:
-🚨 Transaction Count vs Fraud Rate by Currency (Bar and Line)
- Transaction Response Code Table
-  🌍Fraud Incidence by Merchant Category and Location (Heat map)

<img width="720" height="461" alt="Fraud detection dashboard" src="https://github.com/user-attachments/assets/e00264dc-ee7a-4ead-ab96-65de573d9ef2" />

#  Key Insights
##  Fraud Dectection & Transaction Analysis

| **Insight** | **Detail** |
|-------------|------------|
| 🚨 Critical Fraud Rate | **49.86%** of all transactions are fraudulent — nearly **1 in every 2 transactions**. |
| 🌍 International Risk | International fraud rate (**50.05%**) is slightly higher than local fraud (**49.50%**). |
| 📱 Platform-Agnostic Fraud | Fraud occurs across all devices: **Mobile (51.39%)**, **Desktop (49.14%)**, **Tablet (49.12%)**, indicating no device is inherently safer. |
| 🌐 IP Rotation Confirmed | Every fraudulent transaction originated from a **unique IP address**, suggesting deliberate IP rotation to evade detection. |
| 💳 High-Risk Anomalies | Multiple **first-time, high-value purchases** (~**4,996.70**) were made on accounts with **no prior transaction history**. |
| ❌ Gateway Failures | **2,648 Declined** and **2,655 Failed** transactions compared to only **2,697 Approved** transactions, indicating significant payment gateway issues. |

---

# 📈 Transaction Behaviour Insights

| **Insight** | **Detail** |
|-------------|------------|
| ⏰ Peak Activity | **Wednesday at 10:00 AM** recorded the highest transaction volume. |
| 🏙️ Top City | **Ghaziabad** processed **53 transactions**, generating **127,716.30** in total transaction value. |
| 🏪 Top Merchant | **Yohannan and Sons** generated the highest revenue at **16,623.12**. |
| 💳 Card Distribution | **Visa**, **American Express**, and **Mastercard** each account for approximately **33%** of transactions. |
| 👤 Top Spender | **Trisha Ghose** recorded the highest customer spend at **12,373.36**. |
| 🛒 Transaction Channel | **In-Person:** **51%** • **Online:** **49%**, showing an almost even transaction distribution. |

---

# 💡 Recommendations

| **Risk Vector** | **Recommendation** | **Target Metric** |
|-----------------|--------------------|-------------------|
|  Cross-Border Fraud | Deploy **dynamic geo-fencing** with **real-time Multi-Factor Authentication (MFA)** for all **USD** and **EUR** transactions. | Reduce **50.05%** international fraud rate. |
| 🌐 Zero-History Transactions | Automatically place **high-value first-time purchases** on hold for manual verification. | Prevent stolen-card fraud on new accounts. |
| ⚙️ Gateway Failures | Collaborate with payment processors to resolve **Response Codes 5 & 12**, improving validation and reducing timeouts. | Recover **2,648 declined transactions**. |
| 🌐 IP Rotation | Replace traditional **IP blacklisting** with **device fingerprinting** and **behavioural analytics**. | Improve detection of rotating-IP fraud schemes. |
| 🏪 Merchant Risk | Apply **transaction throttling** to high-risk merchant-city combinations during peak fraud periods. | Reduce fraud concentration in high-risk hotspots. |
| 📱 Device Security | Strengthen **mobile authentication** through adaptive MFA and risk-based verification. | Lower the **51.39%** mobile fraud rate. |

---

## 📌 Executive Summary

- Nearly **50%** of all transactions were fraudulent, highlighting a critical fraud exposure.
- Fraud is **consistent across all devices**, with mobile transactions showing the highest risk.
- **International transactions** present slightly greater fraud risk than domestic transactions.
- Fraudsters consistently use **unique IP addresses**, reducing the effectiveness of traditional IP blocking.
- Payment gateway performance requires immediate attention due to the high volume of failed and declined transactions.
- Implementing **behaviour-based fraud detection**, **adaptive authentication**, and **real-time transaction monitoring** would significantly strengthen fraud prevention while improving legitimate transaction success rates.






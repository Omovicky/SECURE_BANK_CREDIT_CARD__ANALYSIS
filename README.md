# SecureBank Nigeria: Credit Card Risk, Fraud & Performance Analysis

![Status](https://img.shields.io/badge/Status-Completed-green)
![Tools](https://img.shields.io/badge/Tools-Excel%20%7C%20SQL%20Server%20%7C%20Power%20BI-blue)

## Project Background
SecureBank Nigeria identified unusual patterns in credit card transactions and commissioned a full analytical review of 8,000 transactions to understand spending behaviours, detect fraud patterns, and surface actionable insights for the Chief Risk Officer.

## Problem Statement
SecureBank Nigeria has noticed unusual patterns in credit card transactions and wants to analyse spending behaviours, identify transaction anomalies, and understand customer usage patterns better. This analysis will help improve fraud detection protocols and enhance customer service by understanding normal transaction behaviours.

## Objectives
- Analyse transaction behaviour and spending patterns across merchants, cities, card types, and cardholders
- Identify fraud trends, risk exposure, and anomalies across devices, IP addresses, and transaction sources
- Evaluate transaction performance including response codes and declined transactions
- Provide strategic recommendations to reduce fraud and improve operational efficiency

## Dataset
| Property | Detail |
|---|---|
| Source | Kaggle — teamincribo/credit-card-fraud |
| File | credit_card_fraud.csv |
| Rows | 8,000 transactions |
| Columns | 20 original — 23 after cleaning |
| Date Range | January 2020 — December 2023 |
| Currencies | INR, USD, EUR |
Dataset Note: The dataset contains Indian city names and INR currency despite being framed as a Nigerian bank project. INR transactions were treated as local and USD/EUR as international for analytical purposes. This limitation is documented transparently throughout the analysis.

## Tools and Technologies
| Tool | Purpose |
|---|---|
| Microsoft Excel | Data cleaning and transformation |
| SQL Server SSMS | Data import, querying, and answering 15 business questions |
| Power BI Desktop | Interactive dashboard development and visualisation |
| PowerPoint | Presentation of findings to stakeholder audience |

## 15 Business Questions Answered
| Number | Question | Tool |
|---|---|---|
| Q1 | Total transaction amounts and counts over time | SQL and Power BI |
| Q2 | Merchants generating highest transaction revenue | SQL and Power BI |
| Q3 | Most common merchant categories MCC by volume | SQL and Power BI |
| Q4 | Cities with highest transaction activity | SQL and Power BI |
| Q5 | Peak transaction hours and days of the week | SQL and Power BI |
| Q6 | Card types with highest transaction volumes | SQL and Power BI |
| Q7 | Cardholders with highest total spending | SQL and Power BI |
| Q8 | Percentage of transactions flagged as fraudulent | SQL and Power BI |
| Q9 | Patterns in device info and IP addresses linked to fraud | SQL and Power BI |
| Q10 | Transaction amounts by transaction source | SQL and Power BI |
| Q11 | Most frequent response codes for declined transactions | SQL |
| Q12 | Duplicate or repeated transaction IDs | SQL |
| Q13 | International vs local transactions volume and fraud rate | SQL and Power BI |
| Q14 | Transactions with highest amounts relative to cardholder history | SQL |
| Q15 | Correlation between merchant category location and fraud | SQL and Power BI |

## Key Findings

### Fraud Analysis

| Finding | Value |
|---|---|
| Overall fraud rate | 49.86% — nearly 1 in 2 transactions fraudulent |
| Total fraudulent transactions | 3,989 out of 8,000 |
| International fraud rate | 50.05% |
| Local INR fraud rate | 49.50% |
| Highest fraud device | Mobile — 51.39% |
| IP address pattern | Every fraud transaction used a unique IP indicating IP rotation |

### Transaction Behaviour

| Finding | Value |
|---|---|
| Busiest city | Ghaziabad — 53 transactions |
| Top merchant by revenue | Yohannan and Sons — 16,623.12 |
| Top cardholder by spending | Trisha Ghose — 12,373.36 |
| Peak transaction hour | 10 AM |
| Peak transaction day | Wednesday |
| Top card type | Visa — 2,682 transactions |

### Operational Performance

| Finding | Value |
|---|---|
| Approved transactions | 2,697 Response Code 0 |
| Declined transactions | 2,648 Response Code 5 |
| Failed transactions | 2,655 Response Code 12 |
| In-Person fraud rate | 49.22% |
| Online fraud rate | 50.54% |

## Dashboard Preview

### Page 1 — Executive Summary
<img width="720" height="469" alt="Executive Summary Dashbaord" src="https://github.com/user-attachments/assets/9cbb0a7f-9dd0-4aab-9fd0-4923adcf30da" />

### Page 2 — Transaction Behaviour and Spending Analysis
<img width="720" height="446" alt="Transcation behaviour dashboard" src="https://github.com/user-attachments/assets/2a8648ba-f0f5-4100-90de-1bae0df05c8e" />







### Page 3 — Fraud Detection Anomalies and Risk Insights


![Fraud Detection](powerbi/dashboard_screenshots/fraud_detection.png)






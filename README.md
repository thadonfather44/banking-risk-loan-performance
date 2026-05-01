Credit Risk Analytics

Overview
This is an end to end Data Engineering/Analytics project built around a retail banking credit risk use case. The project covers relational database design, SQL analytical querying and interactive dashboard development using MySQL and Power BI.
The goal was to move from raw transactional data to actionable business intelligence that a credit risk team could use to make real lending decisions.

Business Problem
A retail bank cannot effectively manage credit risk without visibility into which customers are likely to default and why. Collections teams waste resources chasing low risk customers while missing high risk ones. Lending policies are applied broadly instead of being targeted at the segments that actually drive defaults.
This project answers one core business question:
Which customers are likely to default on their loans and what drives loan performance across the portfolio?

Tech Stack
MySQL8.0
Schema design and data storage

SQL
Analytical queries and KPI calculation

Power BI Desktop
Interactive dashboard development

DAX
Calculated measures and KPIs
    
Database Design
Three normalised tables with primary and foreign key constraints reflecting how retail banking systems store transactional data in production environments.
customers (1) ----< loans (many)
loans (1) ----< loan_payments (many)

Tables
customers stores demographic and financial profile information per customer including age, province, annual income, employment type and credit score.
loans stores the full loan record per customer including loan type, disbursement date, interest rate, tenure, outstanding balance and current loan status.
loan_payments stores individual payment transactions per loan with payment status recorded as on time, late or missed.

Dataset Size
Customers 50
Loans 69
Loan Payments 47

SQL Analytical Queries
Eleven queries power the full dashboard from KPI calculation through to risk segmentation and the high risk watchlist.
KPI SummaryTotal 
loans, default rate, average interest rate, total exposure

Loan Status Breakdown 
Active vs paid vs default vs under review counts

Default by Credit Band
Default rate across six credit score tiers

Customer by Province
Loan volume, defaults and average income by region

Customer by Age Group
Default rate and borrowing behaviour across age segments

Loan Type Performance
Product level default rate and interest rate analysis

Employment vs Default
Default rate by employment type

Income Band vs Default
Default rate across four income tiers

High Risk Watchlist
Customers ranked by defaulted outstanding exposure

Payment Behaviour
On time vs late vs missed payment distribution

Master View
Full joined view used for Power BI live connection

Dashboard Pages
Page 1 - Executive Summary
Portfolio level overview with five live KPI cards covering total loans, total customers, default rate, average interest rate and total exposure. Includes a loan status breakdown donut chart and default rate by credit score band bar chart for immediate risk visibility.

Page 2 - Risk Analysis
Deep dive into the drivers of default across four dimensions including employment type, age group, income band and loan type. A scatter plot overlays credit score against loan amount coloured by default status to show the relationship between creditworthiness and borrowing behaviour.

Page 3 - Customer Segments
Geographic and demographic segmentation of the portfolio by South African province, age group, income band and employment type. A filled map visual shows default rate intensity by region allowing the credit risk team to identify high risk geographies at a glance.

Page 4 - High Risk Watchlist
Filtered table restricted to CRITICAL and HIGH risk customers only ranked by outstanding defaulted exposure. Conditional formatting on credit score and default rate columns provides instant visual risk grading. Risk Tier and Province slicers allow the collections team to filter by segment.

DAX Measures
Total Loans =
COUNTROWS(banking_risk_db_loans)

Total Customers =
COUNTROWS(banking_risk_db_customers)

Default Rate =
DIVIDE(
    COUNTROWS(FILTER(banking_risk_db_loans, banking_risk_db_loans[loan_status] = "Default")),
    COUNTROWS(banking_risk_db_loans)
)

Avg Interest Rate =
AVERAGE(banking_risk_db_loans[interest_rate])

Total Exposure =
SUM(banking_risk_db_loans[outstanding_balance])

Default Exposure =
CALCULATE(
    SUM(banking_risk_db_loans[outstanding_balance]),
    banking_risk_db_loans[loan_status] = "Default"
)

Avg Credit Score =
AVERAGE(banking_risk_db_customers[credit_score])

Key Findings
Credit score is the strongest predictor of default.
Customers with scores below 580 default at over 60 percent which is double the portfolio average of 31.9 percent. The relationship is consistent across all credit bands confirming credit score as the primary risk signal.
Younger customers carry disproportionate default risk.
The 18 to 25 age group has a default rate of 55 percent driven by lower incomes, limited credit history and smaller loan buffers.
Low income customers default at significantly higher rates.
Customers earning under R150k per year default at 62 percent compared to only 9 percent for customers earning over R500k.
Self employed customers are riskier than salaried customers.
Self employed customers default at 38.5 percent versus 32.1 percent for salaried customers due to income instability.
KwaZulu Natal and Limpopo carry the highest regional risk.
KwaZulu Natal has a 42 percent default rate and Limpopo has a 50 percent default rate compared to Western Cape at only 11 percent.
Personal loans drive the majority of defaults.
Personal loans represent 68 percent of all defaults despite being only 45 percent of total loan volume.
Home loans are the safest product in the portfolio.
The home loan segment has a default rate of only 5.6 percent backed by collateral and strong borrower profiles.

Recommendations
Set a minimum credit score of 620 for unsecured personal loans.
Customers below this threshold default at rates that make unsecured lending unprofitable.
Implement an Early Warning System triggered at the first missed payment.
Early intervention gives the collections team the maximum window to engage before a loan deteriorates further.
Offer debt restructuring to critical tier customers.
Eight CRITICAL tier customers carry over R280000 in combined defaulted exposure. Proactive restructuring reduces write off risk at a lower cost than collections.
Enhance income verification for self employed applicants.
Requiring three to six months of bank statements and a minimum average monthly income threshold improves credit quality in this segment.
Expand home loan marketing in Western Cape and high income segments.
Western Cape customers average a credit score of 745 and home loans carry the lowest default rate in the portfolio making this a strong low risk growth opportunity.
Introduce smaller initial credit limits for the 18 to 25 age group.
Pairing first time borrowers with lower limits and financial literacy resources reduces long term default risk while building a loyal customer base.

How to Run This Project
Step 1 - Set up the database
mysql -u root -p < database/01_schema_and_data.sql

Step 2 - Verify the data loaded correctly
USE banking_risk_db;

SELECT 'customers' AS tbl, COUNT(*) AS rows FROM customers
UNION ALL
SELECT 'loans', COUNT(*) FROM loans
UNION ALL
SELECT 'loan_payments', COUNT(*) FROM loan_payments;

Step 3 - Connect Power BI to MySQL

Download MySQL Connector/NET from dev.mysql.com
Open Power BI Desktop
Get Data - MySQL Database
Server: localhost
Database: banking_risk_db
Load all three tables

Alternative - Use the CSV export
If you do not want to set up MySQL locally open Power BI and connect to exports/tableau_master.csv instead. All dashboard visuals will load from the flat file.



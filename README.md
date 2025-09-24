# Assignment-1
This assignment overview  mastery of PL/SQL window functions by solving a realistic business problem, implementing analytical queries, and documenting results in a professional GitHub repository


Names:Ishimwe Daniel 
ID:27296
PL/SQL Assignment 
Step 1: Problem Definition
Business Context
Company: RwandaCoffee Ltd - Premium coffee retailer and distributor Industry: Coffee retail and wholesale distribution Department: Sales Analytics
Data Challenge
RwandaCoffee Ltd operates 15 retail locations across Rwanda's major cities and serves 200+ wholesale clients. The company struggles to identify top-performing products by region, track customer purchasing patterns, and optimize inventory based on seasonal trends. Sales data is scattered across multiple systems, making it difficult to perform comparative analysis and forecast demand accurately.
Expected Outcome
Develop analytical insights to identify top products per region/quarter, segment customers for targeted marketing campaigns, track sales momentum through running totals and growth rates, and provide data-driven recommendations for inventory optimization and regional expansion strategies.
Step 2: Success Criteria (5 Measurable Goals)
1.	Top 5 products per region/quarter → RANK() function
•	Identify best-selling products by sales volume in each region quarterly
2.	Running monthly sales totals → SUM() OVER() function
•	Track cumulative sales performance throughout the year
3.	Month-over-month growth analysis → LAG()/LEAD() functions
•	Calculate percentage growth/decline between consecutive months
4.	Customer quartiles segmentation → NTILE(4) function
•	Segment customers into 4 groups based on purchase frequency/value
5.	3-month moving averages → AVG() OVER() function
•	Smooth out seasonal fluctuations to identify underlying trends



Step 3: Database Schema Design
 	Database Stracture (RwandaCoffe Ltd)
 
	Table for Customers, Products, and transactions

 
                      
                      
 	SQL Schema Creation
 
 
ER Diagram Description
•	customers (1) ←→ (M) transactions
•	products (1) ←→ (M) transactions
•	transactions acts as junction table with additional sales data
Step 4: Window Functions Implementation 
1. Ranking Functions 
•	ROW_NUMBER() - Sequential ranking
-- Top customers by total revenue with sequential ranking
SELECT 
    c.name,
    c.region,
    SUM(t.amount) as total_revenue,
    ROW_NUMBER() OVER (ORDER BY SUM(t.amount) DESC) as revenue_rank
FROM customers c
JOIN transactions t ON c.customer_id = t.customer_id
GROUP BY c.customer_id, c.name, c.region
ORDER BY total_revenue DESC;
•	RANK() - Products per region
-- Top 5 products per region with tied rankings
SELECT region, product_name, total_sales, product_rank
FROM (
    SELECT 
        c.region,
        p.name as product_name,
        SUM(t.amount) as total_sales,
        RANK() OVER (PARTITION BY c.region ORDER BY SUM(t.amount) DESC) as product_rank
    FROM transactions t
    JOIN customers c ON t.customer_id = c.customer_id
    JOIN products p ON t.product_id = p.product_id
    GROUP BY c.region, p.product_id, p.name
)
WHERE product_rank <= 5;
DENSE_RANK() - Customer performance tiers
-- Customer performance tiers without gaps
SELECT 
    c.name,
    COUNT(t.transaction_id) as purchase_frequency,
    DENSE_RANK() OVER (ORDER BY COUNT(t.transaction_id) DESC) as performance_tier
FROM customers c
LEFT JOIN transactions t ON c.customer_id = t.customer_id
GROUP BY c.customer_id, c.name;
2. Aggregate Functions 
Running Totals - SUM() OVER()
-- Monthly running sales totals
SELECT 
    TO_CHAR(sale_date, 'YYYY-MM') as month,
    SUM(amount) as monthly_sales,
    SUM(SUM(amount)) OVER (ORDER BY TO_CHAR(sale_date, 'YYYY-MM') 
                          ROWS UNBOUNDED PRECEDING) as running_total
FROM transactions
GROUP BY TO_CHAR(sale_date, 'YYYY-MM')
ORDER BY month;
•	Moving Averages - AVG() OVER()
-- 3-month moving average of sales
SELECT 
    TO_CHAR(sale_date, 'YYYY-MM') as month,
    SUM(amount) as monthly_sales,
    AVG(SUM(amount)) OVER (ORDER BY TO_CHAR(sale_date, 'YYYY-MM') 
                          ROWS 2 PRECEDING) as three_month_avg
FROM transactions
GROUP BY TO_CHAR(sale_date, 'YYYY-MM')
ORDER BY month;
3. Navigation Functions 
•	LAG() - Month-over-month growth
-- Month-over-month sales growth percentage
SELECT 
    month,
    monthly_sales,
    previous_month_sales,
    CASE 
        WHEN previous_month_sales IS NOT NULL THEN
            ROUND(((monthly_sales - previous_month_sales) / previous_month_sales) * 100, 2)
    END as growth_percentage
FROM (
    SELECT 
        TO_CHAR(sale_date, 'YYYY-MM') as month,
        SUM(amount) as monthly_sales,
        LAG(SUM(amount)) OVER (ORDER BY TO_CHAR(sale_date, 'YYYY-MM')) as previous_month_sales
    FROM transactions
    GROUP BY TO_CHAR(sale_date, 'YYYY-MM')
)
ORDER BY month;
•	LEAD() - Forward-looking analysis
-- Compare current month with next month
SELECT 
    month,
    monthly_sales,
    LEAD(SUM(amount)) OVER (ORDER BY TO_CHAR(sale_date, 'YYYY-MM')) as next_month_sales
FROM transactions
GROUP BY TO_CHAR(sale_date, 'YYYY-MM')
ORDER BY month;
4. Distribution Functions 
•	NTILE(4) - Customer quartiles
-- Segment customers into 4 quartiles by purchase value
SELECT 
    c.name,
    SUM(t.amount) as total_spent,
    NTILE(4) OVER (ORDER BY SUM(t.amount)) as customer_quartile
FROM customers c
JOIN transactions t ON c.customer_id = t.customer_id
GROUP BY c.customer_id, c.name
ORDER BY total_spent DESC;
•	CUME_DIST() - Cumulative distribution
-- Cumulative distribution of customer spending
SELECT 
    c.name,
    SUM(t.amount) as total_spent,
    ROUND(CUME_DIST() OVER (ORDER BY SUM(t.amount)) * 100, 2) as percentile
FROM customers c
JOIN transactions t ON c.customer_id = t.customer_id
GROUP BY c.customer_id, c.name
ORDER BY total_spent DESC;
Step 5: GitHub Repository Structure
Repository Name
plsql-window-functions-[your-lastname]-[your-firstname]
File Organization
/
├── README.md
├── sql-scripts/
│   ├── 01-schema-creation.sql
│   ├── 02-sample-data.sql
│   ├── 03-ranking-functions.sql
│   ├── 04-aggregate-functions.sql
│   ├── 05-navigation-functions.sql
│   └── 06-distribution-functions.sql
├── screenshots/
│   ├── ranking-results/
│   ├── aggregate-results/
│   ├── navigation-results/
│   └── distribution-results/
├── documentation/
│   ├── er-diagram.png
│   └── business-analysis.md
└── references.md
README.md Template
# RwandaCoffee Analytics - PL/SQL Window Functions Project

## Business Problem
Analyze sales patterns and customer behavior for RwandaCoffee Ltd to optimize inventory and marketing strategies across 15 retail locations in Rwanda.

## Database Schema
[Include ER diagram and table descriptions]

## Window Functions Analysis
[Summary of each function category with key insights]

## Key Findings
1. [Top insight from ranking analysis]
2. [Key trend from aggregate analysis]
3. [Important pattern from navigation analysis]

## Business Recommendations
[Data-driven recommendations]

## References
[List all 10+ sources]

## Academic Integrity Statement
All sources were properly cited. Implementations and analysis represent original work. No AI-generated content was copied without attribution or adaptation.
Step 6: Results Analysis Framework
1. Descriptive Analysis - What Happened?
•	Sales Patterns: Identify peak sales months, top-performing regions
•	Product Performance: List best/worst selling products by category
•	Customer Behavior: Describe purchase frequency distributions
•	Trends: Document seasonal fluctuations and growth patterns
2. Diagnostic Analysis - Why Did This Happen?
•	Regional Variations: Analyze why certain regions outperform others
•	Product Success Factors: Correlate product performance with pricing, seasonality
•	Customer Segmentation: Explain purchasing behavior differences between quartiles
•	Growth Drivers: Identify factors contributing to month-over-month changes
3. Prescriptive Analysis - What Should Be Done?
•	Inventory Optimization: Recommend stock levels by region and season
•	Marketing Strategy: Suggest targeted campaigns for different customer quartiles
•	Product Strategy: Propose product mix adjustments based on performance
•	Expansion Plans: Recommend regions for new store locations
Step 7: References (Minimum 10 Sources)
Example Reference Categories:
1.	Oracle PL/SQL Window Functions Documentation
2.	Database design best practices articles
3.	Business analytics case studies
4.	Coffee industry market reports
5.	Rwanda economic and demographic data
6.	SQL performance optimization guides
7.	Data visualization best practices
8.	Academic papers on customer segmentation
9.	Retail analytics methodologies
10.	Window functions tutorial resources
Final Submission Checklist
•	[ ] Repository is public and accessible
•	[ ] All SQL scripts run without errors
•	[ ] 20+ clear screenshots included
•	[ ] Professional README with complete analysis
•	[ ] Problem definition, schema, functions, and analysis complete
•	[ ] Academic integrity statement included
•	[ ] Minimum 10 references properly cited
•	[ ] Email submission sent to eric.maniraguha@auca.ac.rw
Submission Email Template
Subject: INSY 8311 - PL/SQL Window Functions Assignment - [Your Name]
Body: Repository Link: [Your GitHub URL] Business Problem: Analyze RwandaCoffee Ltd sales data to optimize regional inventory and customer targeting strategies. Key Findings:
1.	[Insert your top finding]
2.	[Insert your second key insight] Sources Consulted: [Number] references
[Your Name] [Student ID]


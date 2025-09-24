# Assignment-1
This assignment overview  mastery of PL/SQL window functions by solving a realistic business problem, implementing analytical queries, and documenting results in a professional GitHub repository



PL/SQL Assignment 

Step 1: Problem Definition

🔧Business Context

Company: RwandaCoffee Ltd - Premium coffee retailer and distributor Industry: Coffee retail and wholesale distribution Department: Sales Analytics

📍Data Challenge

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

   <img width="465" height="201" alt="image" src="https://github.com/user-attachments/assets/0717202f-b348-4be9-a730-b55211a10947" />

   	Table for Customers, Products, and transactions

   <img width="376" height="319" alt="image" src="https://github.com/user-attachments/assets/f0285767-e0ea-49b0-beca-e5a9c4dd9cb1" /><br>
   
   <img width="378" height="251" alt="image" src="https://github.com/user-attachments/assets/6bacb0be-62b4-4096-8c70-7c64184d6710" /><br>

   <img width="386" height="262" alt="image" src="https://github.com/user-attachments/assets/73d94b57-45a7-44f8-8aac-ada7884fe233" /><br>


       	SQL Schema Creation

   <img width="389" height="217" alt="image" src="https://github.com/user-attachments/assets/ac5087db-fed3-4f15-a114-3ebb8dabd039" /><br>
   

   <img width="390" height="153" alt="image" src="https://github.com/user-attachments/assets/fd6c7ac7-44b4-443e-a0a5-d84fbd6de577" /><br>

   ER Diagram 

   <img width="333" height="325" alt="image" src="https://github.com/user-attachments/assets/e6bb89b2-1d38-4a0e-bbb2-d1158d093eb4" />

ER Diagram Descriptions

•	customers (1) ←→ (M) transactions

•	products (1) ←→ (M) transactions

•	transactions acts as junction table with additional sales data

Step 4: Window Functions Implementation 

1. Ranking Functions
   
•	ROW_NUMBER() - Sequential ranking

-- Top customers by total revenue with sequential ranking--

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

-- Top 5 products per region with tied rankings--

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

-- Customer performance tiers without gaps--

SELECT 

    c.name,
    COUNT(t.transaction_id) as purchase_frequency,
    DENSE_RANK() OVER (ORDER BY COUNT(t.transaction_id) DESC) as performance_tier
    
FROM customers c

LEFT JOIN transactions t ON c.customer_id = t.customer_id

GROUP BY c.customer_id, c.name;

2. Aggregate Functions
   
Running Totals - SUM() OVER()

-- Monthly running sales totals--

SELECT 

    TO_CHAR(sale_date, 'YYYY-MM') as month,
    SUM(amount) as monthly_sales,
    SUM(SUM(amount)) OVER (ORDER BY TO_CHAR(sale_date, 'YYYY-MM') 
                          ROWS UNBOUNDED PRECEDING) as running_total
                          
FROM transactions

GROUP BY TO_CHAR(sale_date, 'YYYY-MM')

ORDER BY month;

•	Moving Averages - AVG() OVER()

-- 3-month moving average of sales--

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

-- Month-over-month sales growth percentage--

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

-- Compare current month with next month--

SELECT 

    month,
    monthly_sales,
    LEAD(SUM(amount)) OVER (ORDER BY TO_CHAR(sale_date, 'YYYY-MM')) as next_month_sales
    
FROM transactions

GROUP BY TO_CHAR(sale_date, 'YYYY-MM')

ORDER BY month;

4. Distribution Functions
   
•	NTILE(4) - Customer quartiles

-- Segment customers into 4 quartiles by purchase value--

SELECT 

    c.name,
    SUM(t.amount) as total_spent,
    NTILE(4) OVER (ORDER BY SUM(t.amount)) as customer_quartile
    
FROM customers c

JOIN transactions t ON c.customer_id = t.customer_id

GROUP BY c.customer_id, c.name

ORDER BY total_spent DESC;

•	CUME_DIST() - Cumulative distribution

-- Cumulative distribution of customer spending--

SELECT

    c.name,
    SUM(t.amount) as total_spent,
    ROUND(CUME_DIST() OVER (ORDER BY SUM(t.amount)) * 100, 2) as percentile
    
FROM customers c

JOIN transactions t ON c.customer_id = t.customer_id

GROUP BY c.customer_id, c.name

ORDER BY total_spent DESC;

References:https://www.naeb.gov.rw/publications/reports












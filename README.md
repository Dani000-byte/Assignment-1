# Window-Function-Ishimwe Daniel
This assignment overview  mastery of PL/SQL window functions by solving a realistic business problem, implementing analytical queries, and documenting results in a professional GitHub repository



PL/SQL Assignment 

Step 1: Problem Definition

🔧Business Context

Company: RwandaCoffee Ltd - Premium coffee retailer and distributor Industry: Coffee retail and wholesale distribution Department: Sales Analytics

📍Data Challenge

RwandaCoffee Ltd operates 15 retail locations across Rwanda's major cities and serves 200+ wholesale clients. The company struggles to identify top-performing products by region, track customer purchasing patterns, and optimize inventory based on seasonal trends. Sales data is scattered across multiple systems, making it difficult to perform comparative analysis and forecast demand accurately.

Expected Outcome

Develop analytical insights to identify top products per region/quarter, segment customers for targeted marketing campaigns, track sales momentum through running totals and growth rates, and provide data-driven recommendations for inventory optimization and regional expansion strategies.

References:https://www.naeb.gov.rw/publications/reports

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
   
   Data inserted for customers table 

   <img width="1174" height="520" alt="image" src="https://github.com/user-attachments/assets/a1916901-e655-4bdd-86a1-8e2d02dd1596" /><br>

   Queries for Customer

     INSERT INTO customers (customer_id, name, region, registration_date, customer_type)
     VALUES 
      (1, 'Alice Uwase', 'Kigali', '2024-01-15', 'Retail'),
      (2, 'John Habimana', 'Musanze', '2024-02-10', 'Wholesale'),
      (3, 'Claire Mukamana', 'Huye', '2024-03-05', 'Retail'),
      (4, 'Eric Nshimiyimana', 'Rubavu', '2024-04-20', 'Wholesale'),
      (5, 'Sarah Ingabire', 'Nyagatare', '2024-05-12', 'Retail'),
      (6, 'David Mugisha', 'Gisenyi', '2024-06-01', 'Retail'),
      (7, 'Aline Uwamahoro', 'Rwamagana', '2024-06-18', 'Wholesale'),
      (8, 'Patrick Niyonzima', 'Butare', '2024-07-05', 'Retail'),
      (9, 'Diane Iradukunda', 'Karongi', '2024-07-22', 'Wholesale'),
      (10, 'Samuel Byiringiro', 'Muhanga', '2024-08-10', 'Retail');

   
   <img width="378" height="251" alt="image" src="https://github.com/user-attachments/assets/6bacb0be-62b4-4096-8c70-7c64184d6710" /><br>

   <img width="1121" height="347" alt="image" src="https://github.com/user-attachments/assets/fcf90333-3a98-450c-acbe-290e19ccd345" /><br>
   
   Querie for products
   
    INSERT INTO products (product_id, product_name, product_category, unit_price)
    VALUES
         (1001, 'Arabica Premium Bourbon', 'Premium', 3500),
         (1002, 'Arabica Standard Bourbon', 'Standard', 2800),
         (1003, 'Robusta Green Beans', 'Standard', 2200),
         (1004, 'Espresso Blend', 'Premium', 4000),
         (1005, 'House Coffee Mix', 'Standard', 2500),
         (1006, 'Cold Brew Pack', 'Specialty', 4200),
         (1007, 'Organic Arabica', 'Premium', 3800),
         (1008, 'Dark Roast Blend', 'Standard', 2700),
         (1009, 'Instant Coffee Classic', 'Standard', 1800),
         (1010, 'Decaf Arabica', 'Specialty', 3600);

   <img width="759" height="564" alt="image" src="https://github.com/user-attachments/assets/bc18f5cf-f1ca-4b5a-893a-c5c9af5c6c24" /><br>

   <img width="386" height="262" alt="image" src="https://github.com/user-attachments/assets/73d94b57-45a7-44f8-8aac-ada7884fe233" /><br>


       	SQL Schema Creation

   <img width="389" height="217" alt="image" src="https://github.com/user-attachments/assets/ac5087db-fed3-4f15-a114-3ebb8dabd039" /><br>
   

   <img width="390" height="153" alt="image" src="https://github.com/user-attachments/assets/fd6c7ac7-44b4-443e-a0a5-d84fbd6de577" /><br>
   
   Table creation for sale_transactions

   <img width="1113" height="313" alt="image" src="https://github.com/user-attachments/assets/9f208561-9787-4129-b4e7-cad4d5bc88d8" /><br>

   queries for sale_transactions

     INSERT INTO transactions (transaction_id, customer_id, product_id, transaction_date, quantity_kg, unit_price, total_amount)
    VALUES
         (1, 1, 1001, '2024-01-20', 5, 3500, 17500),
         (2, 2, 1003, '2024-02-12', 10, 2200, 22000),
         (3, 3, 1002, '2024-03-07', 3, 2800, 8400),
         (4, 4, 1004, '2024-04-25', 8, 4000, 32000),
         (5, 5, 1005, '2024-05-14', 2, 2500, 5000),
         (6, 6, 1007, '2024-06-05', 6, 3800, 22800),
         (7, 7, 1006, '2024-06-20', 4, 4200, 16800),
         (8, 8, 1008, '2024-07-08', 7, 2700, 18900),
         (9, 9, 1009, '2024-07-23', 12, 1800, 21600),
         (10, 10, 1010, '2024-08-12', 5, 3600, 18000);


   <img width="916" height="582" alt="image" src="https://github.com/user-attachments/assets/987ea6c5-ee5f-4de9-a26d-57270731ed4d" /><br>



   ER Diagram 

  <img width="624" height="579" alt="image" src="https://github.com/user-attachments/assets/089c9caa-8e30-4ae5-94b6-84999bc72e9d" />

ER Diagram Descriptions

CUSTOMERS ||--o{ TRANSACTIONS → One customer can have many transactions.

PRODUCTS ||--o{ TRANSACTIONS → One product can appear in many transactions.

📰Reference:https://docs.oracle.com/en/database/oracle/oracle-database/23/sqlrf/Analytic-Functions.html?

Step 4: Window Functions Implementation 

1. Ranking Functions
   
•	ROW_NUMBER() - Sequential ranking

-- Top customers by total revenue with sequential ranking--

    SELECT 
    c.region,
    QUARTER(t.transaction_date) AS sales_quarter,
    p.product_name,
    SUM(t.quantity_kg) AS total_sold,
    RANK() OVER (
        PARTITION BY c.region, QUARTER(t.transaction_date)
        ORDER BY SUM(t.quantity_kg) DESC
    ) AS product_rank
      FROM transactions t
     JOIN customers c ON t.customer_id = c.customer_id
    JOIN products p ON t.product_id = p.product_id
    GROUP BY c.region, sales_quarter, p.product_name
    ORDER BY c.region, sales_quarter, product_rank;

<img width="617" height="414" alt="image" src="https://github.com/user-attachments/assets/986c1e8c-83c4-40a8-974c-30e664219e4b" />

2.	Running monthly sales totals → SUM() OVER() function

Running Monthly Sales Totals

    SELECT 
    DATE_FORMAT(transaction_date, '%Y-%m') AS sales_month,
    SUM(total_amount) AS monthly_sales,
    SUM(SUM(total_amount)) OVER (
        ORDER BY DATE_FORMAT(transaction_date, '%Y-%m')
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS running_total
    FROM transactions
    GROUP BY sales_month
    ORDER BY sales_month;

<img width="419" height="274" alt="image" src="https://github.com/user-attachments/assets/d89eb79a-c86b-4710-814c-8e5f6ddfa565" />

3.	Month-over-month growth analysis → LAG()/LEAD() functions

         SELECT 
          DATE_FORMAT(transaction_date, '%Y-%m') AS sales_month,
          SUM(total_amount) AS monthly_sales,
          LAG(SUM(total_amount)) OVER (ORDER BY DATE_FORMAT(transaction_date, '%Y-%m')) AS prev_month_sales,
        ROUND(
        ( (SUM(total_amount) - LAG(SUM(total_amount)) OVER (ORDER BY DATE_FORMAT(transaction_date, '%Y-%m')))
          / LAG(SUM(total_amount)) OVER (ORDER BY DATE_FORMAT(transaction_date, '%Y-%m')) ) * 100, 2
        ) AS month_growth_percent
         FROM transactions
        GROUP BY sales_month
        ORDER BY sales_month;

   4.	Customer quartiles segmentation → NTILE(4) function
   
•	Segment customers into 4 groups based on purchase frequency/value

    SELECT 
    c.customer_id,
    c.name,
    COUNT(t.transaction_id) AS total_purchases,
    SUM(t.total_amount) AS total_spent,
    NTILE(4) OVER (ORDER BY SUM(t.total_amount) DESC) AS quartile_segment
    FROM customers c
    JOIN transactions t ON c.customer_id = t.customer_id
    GROUP BY c.customer_id, c.name
    ORDER BY quartile_segment, total_spent DESC;

<img width="683" height="355" alt="image" src="https://github.com/user-attachments/assets/54794a5c-3755-492a-9d97-afa88862f8c5" />

5.	3-month moving averages → AVG() OVER() function
    
•	Smooth out seasonal fluctuations to identify underlying trends

    SELECT 
    DATE_FORMAT(transaction_date, '%Y-%m') AS sales_month,
    SUM(total_amount) AS monthly_sales,
    ROUND(
        AVG(SUM(total_amount)) OVER (
            ORDER BY DATE_FORMAT(transaction_date, '%Y-%m')
            ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
        ), 2
    ) AS moving_avg_3m
    FROM transactions
    GROUP BY sales_month
    ORDER BY sales_month;

<img width="1101" height="473" alt="image" src="https://github.com/user-attachments/assets/a0d3c765-b7e5-45a2-bec8-38b9cfaed04e" />




   





      



   

    

    





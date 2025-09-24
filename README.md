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




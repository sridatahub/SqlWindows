SQL Window Functions on Sales Dataset

This project explores how to use powerful SQL features called "window functions" to analyze sales data.

**Key Features:**

* **OVER clause:** Defines the "window" of rows for calculations.

* **PARTITION BY:** Divides data into subsets for separate calculations.

* **ORDER BY:** Specifies the order of rows within each subset.

We'll be working with a table named "sales1" that stores information about each sale, including:

**Table Schema**

The `sales1` table has the following columns:

* **Product Name:** The name of the item sold.
* **Category    :** The type of product (e.g., "Electronics," "Clothing").
* **Customer ID :** A unique identifier for each customer.
* **Sale Date   :** The date when the sale occurred.
* **Amount      :** The total price of the sale.
  
 **Table Creation**
```sql 
    CREATE TABLE sales1(
    sale_id INT PRIMARY KEY,
    product_name VARCHAR(50),
    category VARCHAR(50),
    customer_id INT,
    sale_date DATE,
    amount DECIMAL(10, 2)
);
```
⚙️ SQL Window Functions Applied
1. Rank Products by Sales Amount:
   
Imagine you have different shelves in a store, each holding a different type of product (like "Electronics," "Clothing," etc.). This query figures out which product on each shelf sold the most, second-most, and so on.
```sql
select 
    product_name,
    category,
    amount,
    rank() over(partition by category order by amount desc) as rank_as_Category
from sales1;
```
2. Assign a Row Number to Each Sale:

Imagine you have a list of sales for each product category (like "Electronics," "Clothing," etc.). This query assigns a unique number to each sale within each category based on the order in which they happened.
```sql
select 
    sale_id,
    product_name,
    category,
    row_number() over(partition by category order by sale_date) as sale_num
from sales1;
```

3. Calculate Running Total of Sales:
   
This query calculates how much each customer has spent with the company up to a certain point.
It tracks the total spending for each customer after every purchase they make.
```
select
    sale_id,
    customer_id,
    amount,
    sum(amount) over(partition by customer_id order by amount desc) as running_total
from sales1;
```
4. Identify Previous and Next Sale Amounts:
   
This query finds the sales amount of the very next sale that happened in a specific product category after a particular sale.
It helps you see the sequence of sales amounts within a product category.
```sql
select 
    s1.category,
    s1.sale_id,
    s1.amount,
    coalesce(
        (select s2.amount 
         from sales1 s2 
         where s2.category = s1.category and s2.sale_id > s1.sale_id 
         order by s2.sale_id asc 
         limit 1),
        0
    ) as next_amount
from sales1 s1;
```
5. Compute Cumulative Average Sales:
   
It shows how the average sales amount for each category changes as more sales happen over time.
```sql
select 
    sale_date,
    category,
    amount,
    round(avg(amount) over(partition by category order by sale_date),2) as cummulative_avg
from sales1;
```

6. Find Top N Sales in Each Category:
   
This helps identify the best-selling products within each category, which can be valuable for marketing, inventory management, and product planning.
```sql
with ranked_sale as(
    select product_name,category,amount,
        row_number() over(partition by category order by amount desc) as ranking 
    from sales1
    )
select product_name,category,amount
from ranked_sale
where ranking<=3;
```

7. Percent Rank of Sales:
   
It helps you understand how a specific sale compares to the overall sales performance of other products within the same category.
```sql
select sale_id,
    category,
    amount,
    percent_rank() over(partition by category order by amount desc) as perc_rank
from sales1;
```
8. Calculate the Difference from Average Amount:
   
It helps you understand how much each sale's amount is above or below the typical sales amount for its category.
```sql
select 
    sale_id,product_name,amount,
    round((avg(amount) over(partition by category)),2) as avg_amount,
    round((amount-avg(amount) over(partition by category)),2) as diff_from_avg
from sales1;
```
9. NTILE for Dividing Sales into Quartiles:
    
This helps you understand the distribution of sales amounts across the entire dataset. You can see which sales fall into the lower, middle, and upper ranges of sales amounts.
```sql
select 
    sale_id,
    product_name,
    amount,
    ntile(4) over(order by amount desc) as quartile
from sales1;
```
10. Compute First and Last Sale in Each Category:
    
It helps you see the range of sales amounts that have occurred within each category over time.
```sql
SELECT
    category,
    sale_date,
    amount,
    FIRST_VALUE(amount) OVER (PARTITION BY category ORDER BY sale_date) AS first_sale,
    LAST_VALUE(amount) OVER (PARTITION BY category ORDER BY sale_date ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS last_sale
FROM sales1;
```

💬 Contact
Author: Chavala Srikanth
Email: srikanthchavala2424@gmail.com
GitHub: sridatahub







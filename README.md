SQL Windows Functions on Sales Dataset
📚 Overview
This project demonstrates the application of advanced SQL window functions on a sample sales dataset. The dataset, stored in the sales1 table, includes sales data with details like product name, category, customer ID, sale date, and amount. These functions enable efficient data analysis, ranking, cumulative calculations, and more.
🛠️ Table Schema
The sales1 table is created using the following structure:
'''sql 
    CREATE TABLE sales1(
    sale_id INT PRIMARY KEY,
    product_name VARCHAR(50),
    category VARCHAR(50),
    customer_id INT,
    sale_date DATE,
    amount DECIMAL(10, 2)
);
'''
⚙️ SQL Window Functions Applied
1. Rank Products by Sales Amount
Ranks products within each category based on their sales amount in descending order.
select 
    product_name,
    category,
    amount,
    rank() over(partition by category order by amount desc) as rank_as_Category
from sales1;
2. Assign a Row Number to Each Sale
Assigns a sequential row number to each sale within its category, ordered by the sale date.

select 
    sale_id,
    product_name,
    category,
    row_number() over(partition by category order by sale_date) as sale_num
from sales1;


3. Calculate Running Total of Sales
Calculates the cumulative sales total for each customer, ordered by the sales amount.

select
    sale_id,
    customer_id,
    amount,
    sum(amount) over(partition by customer_id order by amount desc) as running_total
from sales1;

4. Identify Previous and Next Sale Amounts
Finds the next sale amount for a given category and sale ID.

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

5. Compute Cumulative Average Sales
Calculates the cumulative average sales amount for each category over time.

select 
    sale_date,
    category,
    amount,
    round(avg(amount) over(partition by category order by sale_date),2) as cummulative_avg
from sales1;


6. Find Top N Sales in Each Category
Extracts the top 3 sales for each category based on the sales amount.

with ranked_sale as(
    select product_name,category,amount,
        row_number() over(partition by category order by amount desc) as ranking 
    from sales1
    )
select product_name,category,amount
from ranked_sale
where ranking<=3;


7. Percent Rank of Sales
Calculates the percentile rank of each sale amount within its category.

select sale_id,
    category,
    amount,
    percent_rank() over(partition by category order by amount desc) as perc_rank
from sales1;

8. Calculate the Difference from Average Amount
Finds the difference between each sale's amount and the average amount in its category.

select 
    sale_id,product_name,amount,
    round((avg(amount) over(partition by category)),2) as avg_amount,
    round((amount-avg(amount) over(partition by category)),2) as diff_from_avg
from sales1;

9. NTILE for Dividing Sales into Quartiles
Divides sales amounts into four quartiles.

select 
    sale_id,
    product_name,
    amount,
    ntile(4) over(order by amount desc) as quartile
from sales1;

10. Compute First and Last Sale in Each Category
Finds the first and last sale amount for each category based on sale dates.

select
    category,
    sale_date,
    amount,
    first_value(amount) over(partition by category order by sale_date) as first_sale,
    last_value(amount) over(partition by category order by sale_date rows between unbounded preceding and unbounded following) as last_sale
from sales1;

💬 Contact
Author: Chavala Srikanth
Email: srikanthchavala2424@gmail.com
GitHub: sridatahub







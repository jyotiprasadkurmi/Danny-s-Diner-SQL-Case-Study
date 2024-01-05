# 1. What is the total amount each customer spent at the restaurant?

select 
	customer_id,sum(price) as total_amt_spent 
from sales s  left join menu m
using(product_id)
group by customer_id;

--------------------------------------------------------------------------------------------------------------

# 2. How many days has each customer visited the restaurant?

select 
	customer_id ,
    count(distinct order_date) as no_of_days
from sales 
group by customer_id; 

---------------------------------------------------------------------------------------------------------------

# 3. What was the first item from the menu purchased by each customer?

select 
	customer_id,order_date, product_name
from(	
    select
		*,
		row_number() over(partition by customer_id order by order_date) as rn
	from sales s left join menu m
	using(product_id)
    )t
where t.rn = 1;

----------------------------------------------------------------------------------------------------------------

# 4. What is the most purchased item on the menu and how many times was it purchased by all customers?

select
	product_name,sum(no_of_cnt)
from(
    select  
		customer_id,product_id,product_name,
		count(product_id) as no_of_cnt
	from sales s join menu m
	using(product_id)
	group by customer_id,product_id,product_name
	order by no_of_cnt desc)t
group by product_name;

-----------------------------------------------------------------------------------------------------------------

# 5. Which item was the most popular for each customer?

select
	customer_id,product_name
from(
    select  
		customer_id,product_id,product_name,
		count(product_id) as no_of_cnt,
        rank() over(partition by customer_id order by count(product_id) desc) rnk
	from sales s join menu m
	using(product_id)
	group by customer_id,product_id,product_name)t
where t.rnk = 1;

------------------------------------------------------------------------------------------------------------------

# 6. Which item was purchased first by the customer after they became a member?

with table1 as
	(select * from members m join sales s
	using(customer_id)
	where order_date >= join_date
	order by join_date),

table2 as(
    select  
		*,
		rank() over(partition by customer_id order by order_date) rnk
	from table1 t join menu m
	using(product_id)
    )
    
select * from table2
where rnk = 1;

---------------------------------------------------------------------------------------------------------------------

# 7. Which item was purchased just before the customer became a member?

with table1 as
	(select * from members m join sales s
	using(customer_id)
	where order_date < join_date
	),

table2 as(
    select  
		*,
		rank() over(partition by customer_id order by order_date desc) rnk
	from table1 t join menu m
	using(product_id)
    )
    
select * from table2
where rnk = 1;

---------------------------------------------------------------------------------------------------------------------------

# 8. What is the total items and amount spent for each member before they became a member?

with table1 as
	(select * from members m join sales s
	using(customer_id)
	where order_date < join_date
	),

table2 as(
    select  
		*
	from table1 t join menu m
	using(product_id)
    )
    
select 
	customer_id,
    count(product_id) as total_product,
    sum(price) as amt_spent
from table2
group by customer_id
order by customer_id;

----------------------------------------------------------------------------------------------------------------------------

# 9.  If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?

select 
	customer_id,
    sum(case
		when product_name = 'sushi' then 2*price 
        else price
	end) as Final_points
	
from sales s join menu m
on s.product_id = m.product_id
group by customer_id;

------------------------------------------------------------------------------------------------------------------------------

# 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi how many points do customer A and B have at the end of January?

with table1 as
	(select *
    from members m join sales s
	using(customer_id)
	where order_date >= join_date and order_date <= CAST('2021-01-31' AS DATE)
	),

table2 as(
    select  
		*
	from table1 t join menu m
	using(product_id)

    )
    
select customer_id,
	Sum(CASE
                 When (DATEDIFF(join_date,order_date) between 0 and 7) or (product_ID = 1) Then price * 2
                 Else price
              END) As Points
from table2
group by customer_id
order by customer_id;
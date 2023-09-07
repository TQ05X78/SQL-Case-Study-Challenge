# Case Study 1: Tiny Shop Sales
![IMG_20230907_183508](https://github.com/TanuYadu/SQL-Case-Study-Challenge/assets/66067511/e111f95c-508d-493d-b64e-6330a24b8cb7)

## Questions and Solutions

**1. Which product has the highest price? Only return a single row.**

   ````sql  
	 Select *
     from products
     where price = (Select max(price)from products);
   ````
#### Answer:
| product_id | product_name | price |
| ----------- | ---------- |------------  |
| 13           | Product M        |  70.0   |


***
**2. Which customer has made the most orders?**

````sql
Select * from customers;
Select * from orders;

Select c.first_name, c.last_name, count(o.order_date) as orders  
from customers c
join orders o on o.customer_id = c.customer_id
group by c.first_name, c.last_name
order by orders desc;
````
#### Answer:
| first_name  | last_name | orders | 
| ----------- | -------- | ------| 
| Jane        | Smith    |  2    | 
| John        | Doe      |  2    | 
| Bob         | Johnson  |  2    | 
| Quinn       | Roberts  |  1    | 
| Oliver      | Patterson|  1    | 
| Eva         | Fisher   |  1    | 
| Charlie     | Davis    |  1    | 
| Sophia      | Thomas   |  1    | 
| George      | Harris   |  1    | 
| Lily        | Nelson   |  1    | 
| Ivy         | Jones    |  1    |  
| Kevin       | Miller   |  1    | 
| Alice       | Brown    |  1    | 

*** 
**3. What’s the total revenue per product?**

  ````sql
  Select * from products;
  Select * from order_items;

  Select p.product_name as product_name, 
  sum(p.price * oi.quantity) as total_revenue 
  from products p
  join order_items oi on oi.product_id = p.product_id
  group by product_name
  order by total_revenue desc;
  ````
#### Answer:
| product_name | total_revenue | 
| ------------- | ------ |
| Product M     | 420.00 |
| Product J     | 330.00 |
| Product F     | 210.00 |
| Product L     | 195.00 |
| Product K     | 180.00 |
| Product C     | 160.00 |
| Product I     | 150.00 |
| Product B     | 135.00 |
| Product H     | 135.00 |
| Product G     | 120.00 |
| Product E     | 90.00  |
| Product D     | 75.00  |
| Product A     | 50.00  |




 ***
 **4. Find the day with the highest revenue.**

  ````sql
  Select  
  sum(p.price * oi.quantity) as total_revenue,
  to_char(o.order_date, 'Day') as Day
  from products p
  join order_items oi on oi.product_id = p.product_id
  join orders o on o.order_id = oi.order_id
  group by o.order_date
  order by total_revenue desc;
  ````
#### Answer:
| total_revenue | day      | 
| ------------- | ---------|
| 340.00        | Tuesday  |
| 285.00        | Wednesday|
| 275.00        | Thursday |
| 225.00        | Monday   |
| 185.00        | Saturday |
| 145.00        | Monday   |
| 145.00        | Sunday   |
| 140.00        | Tuesday  |
| 85.00         | Sunday   |
| 80.00         | Thursday |
| 80.00         | Friday   |
| 75.00         | Tuesday  |
| 55.00         | Saturday |
| 50.00         | Wednesday|
| 50.00         | Friday   |
| 35.00         | Monday   |


  ***

 **5. Find the first order (by date) for each customer.**

  ````sql
  Select * from customers;
  Select * from orders;

  Select c.customer_id, c.first_name, c.last_name,
  min(o.order_date) as order_date
  from customers c
  left join orders o on c.customer_id = o.customer_id
  group by c.customer_id, c.first_name, c.last_name
  order by c.customer_id;
  ````
  #### Answer:
| first_name  | last_name | order_date | 
| ----------- | -------- | ------| 
| John        | Doe      |  2023-05-01    |
| Jane        | Smith    |  2023-05-02    |
| Bob         | Johnson  |  2023-05-03    |
| Alice       | Brown    |  2023-05-07    |
| Charlie     | Davis    |  2023-05-08    | 
| Eva         | Fisher   |  2023-05-09    | 
| George      | Harris   |  2023-05-10    | 
| Ivy         | Jones    |  2023-05-11    |  
| Kevin       | Miller   |  2023-05-12    | 
| Lily        | Nelson   |  2023-05-13    | 
| Oliver      | Patterson|  2023-05-14    | 
| Quinn       | Roberts  |  2023-05-15    | 
| Sophia      | Thomas   |  2023-05-16    | 




  ***
  **6. Find the top 3 customers who have ordered the most distinct products.**

   ````sql
   Select c.first_name as first_name,
   c.last_name as last_name,
   count(distinct oi.product_id) most_distinct_products 
   from orders o 
   join order_items oi on oi.order_id = o.order_id
   join customers c on c.customer_id = o.customer_id
   join products p on p.product_id = oi.product_id
   group by first_name, last_name
   order by most_distinct_products desc
   limit 3;
   ````
#### Answer:
| first_name  | last_name | most_distinct_products | 
| ----------- | -------- | -----------------| 
| Bob         | Johnson  |  3    |
| John        | Doe      |  3    |
| Jane        | Smith    |  3    |
   ***
  **7. Which product has been bought the least in terms of quantity?**
    
    ````sql
    Select p.product_name as products,
    sum(oi.quantity) as quantity
    from order_items oi 
    join products p on p.product_id = oi.product_id
    group by product_name
    order by quantity;
    ````
#### Answer:
| product_name | quantity | 
| ------------- | ------ |
| Product L     | 3 |
| Product I     | 3 |
| Product H     | 3 |
| Product E     | 3 |
| Product D     | 3 |
| Product G     | 3 |
| Product K     | 3 |
| Product A     | 5 |
| Product M     | 6 |
| Product F     | 6 |
| Product J     | 6 |
| Product C     | 8 |
| Product B     | 9 |



    
  ***
  **8. What is the median order total?**
    
    ````sql
    with cte as (
    Select oi.order_id, 
    sum(oi.quantity*p.price) as revenue,
    row_number() over (order by sum(quantity*price)) as row_number,
    count(*) over() as total_orders
    from order_items oi
    join products p on p.product_id = oi.product_id
    group by oi.order_id)
    Select percentile_disc(0.5) within group (order by revenue) as median_order from cte;
    ````
#### Answer:
| median_order |
|--------------|
|  85.00       |




    
   *** 
    
   **9. For each order, determine if it was ‘Expensive’ (total over 300),‘Affordable’ (total over 100), or ‘Cheap’.**
      
     ````sql    
     with cte as (
     Select o.order_id order_id, sum(oi.quantity * p.price) as revenue  
     from orders o
     join order_items oi on oi.order_id = o.order_id
     join products p on p.product_id = oi.product_id
     group by o.order_id
     order by o.order_id)
     Select order_id, revenue, 
     case 
     when revenue > 300 then 'Expensive'
     when revenue > 100 then 'Affordable' 
     else 'Cheap' end as Affordability
     from cte;	 
     ```` 

#### Answer:
| order_id  | revenue | affordability | 
| --------- | -------- | -----------------| 
| 1         | 35.00  |  Cheap    |
| 2         | 75.00  |  Cheap    |
| 3         | 50.00  |  Cheap    |
| 4         | 80.00  |  Cheap    |
| 5         | 50.00  |  Cheap    |
| 6         | 55.00  |  Cheap    |
| 7         | 85.00  |  Cheap    |
| 8         | 145.00 |  Affordable    |
| 9         | 140.00 |  Affordable    |
| 10        | 285.00 |  Affordable    |
| 11        | 275.00 |  Affordable    |
| 12        | 80.00  |  Cheap    |
| 13        | 185.00 |  Affordable    |
| 14        | 145.00 |  Affordable    |
| 15        | 225.00 |  Affordable    |
| 16        | 340.00 |  Expensive    |


     
     ***
     
    
    
  **10. Find customers who have ordered the product with the highest price.**
      
      
      ````sql
      Select c.first_name as first_name,
      c.last_name as last_name,
      p.price 
      from customers c
      join orders o on o.customer_id = c.customer_id
      join order_items oi on oi.order_id = o.order_id
      join products p on p.product_id = oi.product_id
      where p.price in (Select max(price) from products);
      ````
#### Answer:
| first_name | last_name | price |
|------------|-----------|-------|
| Ivy        |  Jones    | 70.00 |
| Sophia     |  Thomas   | 70.00 |
      
   *** 
   

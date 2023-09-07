

## Questions and Solutions

**1. Which product has the highest price? Only return a single row.**

   ````sql  
	 Select *
     from products
     where price = (Select max(price)from products);
   ````

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
   
   
   **7. Which product has been bought the least in terms of quantity?**

   
    ````sql
    Select p.product_name as products, sum(oi.quantity) as quantity
    from order_items oi 
    join products p on p.product_id = oi.product_id
    group by product_name
    order by quantity;
    ````
   
   
   **8. What is the median order total?**

    
    ````sql
    with cte as (
    Select oi.order_id, sum(oi.quantity*p.price) as revenue,
    row_number() over (order by sum(quantity*price)) as row_number,
    count(*) over() as total_orders
    from order_items oi
    join products p on p.product_id = oi.product_id
    group by oi.order_id)
    
    Select percentile_disc(0.5) within group (order by revenue) as median_order from cte;
    ````
    
   
   **9. For each order, determine if it was ‘Expensive’ (total over 300), ‘Affordable’ (total over 100), or ‘Cheap’.**
      
    
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


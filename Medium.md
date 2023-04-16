### [176. Second Highest Salary](https://leetcode.com/problems/second-highest-salary/)  

```sql
select 
  max(salary) as SecondHighestSalary
from employee
where salary < (select max(salary) from employee)
```

### [177. Nth Highest Salary](https://leetcode.com/problems/nth-highest-salary/)  

```sql
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
  RETURN (
    select distinct salary from (
      select
        id,
        salary,
        dense_rank() over(order by salary desc) as rnk
      from Employee
    ) as foo
    where rnk = N  
  );
END
```

### [178. Rank Scores](https://leetcode.com/problems/rank-scores/)  

```sql
select 
  score,
  dense_rank() over(order by score desc) as "rank"
from scores
order by score desc
```

### [180. Consecutive Numbers](https://leetcode.com/problems/consecutive-numbers/)  

```sql
with t as (
  select
    id,
    num,
    lead(num) over(order by id) as first_lag,
    lead(num, 2) over(order by id) as second_lag
from logs
)
select 
  distinct num as ConsecutiveNums
from t 
where num = first_lag 
 and num = second_lag 
 and first_lag = second_lag;
```

### [184. Department Highest Salary](https://leetcode.com/problems/department-highest-salary/)  

```sql
with t as (
  select 
    d.name as Department,
    e.name as Employee,
    salary,
    dense_rank() over (partition by d.name order by salary desc) as dr
  from employee e
  join department d on e.departmentId = d.id
)
select 
  Department, Employee, Salary
from t
where dr = 1    
```

### [570. Managers with at Least 5 Direct Reports](https://leetcode.com/problems/managers-with-at-least-5-direct-reports/)  

```sql
select 
  t1.name
from Employee t1
 left join Employee t2 on t1.id = t2.managerId
group by 1
having count(t2.managerId) >= 5   
```

### [585. Investments in 2016](https://leetcode.com/problems/investments-in-2016/)  

```sql
select round(sum(tiv_2016), 2) as tiv_2016 from (
  select 
    tiv_2016,
    count(*) over(partition by tiv_2015) as cnt,
    count(*) over(partition by lat, lon) as lat_lon
  from Insurance
)t
where cnt > 1 and lat_lon = 1  
```

### [602. Friend Requests II: Who Has the Most Friends](https://leetcode.com/problems/friend-requests-ii-who-has-the-most-friends/)  

```sql
select id, sum(cnt) as num from (
  select requester_id as id, count(*) as cnt from RequestAccepted group by 1
    union all
  select accepter_id as id, count(*) as cnt from RequestAccepted group by 1
)t
group by 1
order by 2 desc
limit 1 
```

### [608. Tree Node](https://leetcode.com/problems/tree-node/)  

```sql
select 
  id,
  case when p_id is null then 'Root'
    when id in (select p_id from tree) then 'Inner'
    else 'Leaf' end as type
from tree
```

### [626. Exchange Seats](https://leetcode.com/problems/exchange-seats/)  

```sql
select 
  case when mod(id, 2) = 1 and id != (select max(id) from seat) then id + 1 
    when mod(id, 2) = 0 then id - 1 else id end id, 
  student 
from seat 
order by id
```

### [1045. Customers Who Bought All Products](https://leetcode.com/problems/customers-who-bought-all-products/)  

```sql
select
  customer_id
from Customer
group by customer_id
having count(distinct product_key) = (select count(*) from Product)
```

### [1070. Product Sales Analysis III](https://leetcode.com/problems/product-sales-analysis-iii/)  

```sql
select 
  product_id,
  year as first_year,
  quantity,
  price
from Sales s
where exists (
  select * from (
    select product_id, min(year) as min_year 
    from Sales
    group by product_id) t
  where t.product_id = s.product_id and s.year = t.min_year )
```

### [550. Game Play Analysis IV](https://leetcode.com/problems/game-play-analysis-iv/)  

```sql
select round(count(distinct player_id) / (select count(distinct player_id) from Activity), 2) as fraction 
from (
  select 
    player_id,
    event_date - min(event_date) over(partition by player_id) as diff
  from Activity
)t
where diff = 1
```

### [1158. Market Analysis I](https://leetcode.com/problems/market-analysis-i/)  

```sql
select 
  user_id as buyer_id,
  join_date,
  (select count(order_id) from orders 
    where orders.buyer_id = u.user_id and year(order_date) = 2019) as orders_in_2019
from users u
```

### [1164. Product Price at a Given Date](https://leetcode.com/problems/product-price-at-a-given-date/)  

```sql
with t as (
  select distinct
    product_id,
    last_value(new_price) over (partition by product_id order by change_date rows between unbounded preceding and unbounded following) as price
  from Products
  where change_date <= '2019-08-16'
)
select * from t
  union 
select distinct product_id, 10 as price from Products
  where product_id not in (select product_id from t)
order by 1
```

### [1174. Immediate Food Delivery II](https://leetcode.com/problems/immediate-food-delivery-ii/)  

```sql
with fo as (
  select 
    customer_id,
    min(order_date) as order_date
  from Delivery
  group by 1
)
select
  round(100 * sum(case when fo.order_date = customer_pref_delivery_date then 1 else 0 end) / 
    (select count(*) from fo), 2) as immediate_percentage
from fo
join Delivery d on fo.customer_id = d.customer_id and fo.order_date = d.order_date
```

### [1193. Monthly Transactions I](https://leetcode.com/problems/monthly-transactions-i/)  

```sql
select
  date_format(trans_date, '%Y-%m') as month,
  country,
  count(*) as trans_count,
  sum(case when state = 'approved' then 1 else 0 end) as approved_count,
  sum(amount) as trans_total_amount,
  sum(case when state = 'approved' then amount else 0 end) as approved_total_amount
from Transactions
group by 1, 2
```

### [1204. Last Person to Fit in the Bus](https://leetcode.com/problems/last-person-to-fit-in-the-bus/)  

```sql
select person_name from (
  select *,
    sum(weight) over (rows between unbounded preceding and current row) as cumsum
  from Queue
  order by turn
)t
where cumsum <= 1000
order by cumsum desc
limit 1
```

### [1321. Restaurant Growth](https://leetcode.com/problems/restaurant-growth/)  

```sql
with t as (
  select 
  visited_on,
  sum(amount) as amount
from Customer
group by 1
)
select 
  visited_on,
  sum(amount) over(order by visited_on rows between 6 preceding and current row) as amount,
  round(avg(amount) over(order by visited_on rows between 6 preceding and current row), 2) as average_amount
from t
limit 9999
offset 6
```

### [1341. Movie Rating](https://leetcode.com/problems/movie-rating/)  

```sql
(select name as results
from Users u
 join MovieRating mr on u.user_id = mr.user_id
group by 1
order by count(rating) desc, name
limit 1)
  union
(select title as results
from Movies m
 join MovieRating mr on m.movie_id = mr.movie_id
where date_format(created_at, '%Y-%m') = '2020-02'
group by 1
order by avg(rating) desc, title
limit 1)
```

### [1393. Capital Gain/Loss](https://leetcode.com/problems/capital-gainloss/)  

```sql
select
  stock_name,
  sum(case when operation = 'sell' then price end)  -
   sum(case when operation = 'buy' then price end) as capital_gain_loss
from stocks
group by stock_name
```

### [1907. Count Salary Categories](https://leetcode.com/problems/count-salary-categories/)  

```sql
with cte(category) as (
  select 'Low Salary' 
   union
  select 'Average Salary' 
   union
  select 'High Salary'
)
select cte.category, count(t.cat) as accounts_count
from cte
left join (
  select 
   case when income < 20000 then 'Low Salary'
    when income between 20000 and 50000 then 'Average Salary'
    else 'High Salary' end as cat
  from Accounts
)t
on cte.category = t.cat
group by 1
```

### [1934. Confirmation Rate](https://leetcode.com/problems/confirmation-rate/)  

```sql
select 
  s.user_id, 
  case when confirmed_cnt is null then 0 else round(confirmed_cnt / cnt, 2) end as confirmation_rate
from (
  select 
    user_id,
    count(*) as cnt,
    sum(case when action = 'confirmed' then 1 end) as confirmed_cnt
  from Confirmations
  group by 1
) t
 right join Signups s on t.user_id = s.user_id
group by 1
```
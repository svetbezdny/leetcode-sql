### [175. Combine Two Tables](https://leetcode.com/problems/combine-two-tables/description/)  

```sql
select
  firstName,
  lastName,
  city,
  state
from person as p
left join address a on p.personid = a.personid
```

### [181. Employees Earning More Than Their Managers](https://leetcode.com/problems/employees-earning-more-than-their-managers/)  

```sql
select Employee from (
  select e1.name as Employee, e.name as name_man, e1.salary as e_s, e.salary as m_s
  from employee e join employee e1 on e.id = e1.managerId )t
where e_s > m_s
```

### [182. Duplicate Emails](https://leetcode.com/problems/duplicate-emails/)  

```sql
with t as (
  select 
    email,
    count(email) as cnt
  from person
  group by email )
select email
from t
where cnt > 1
```

### [183. Customers Who Never Order](https://leetcode.com/problems/customers-who-never-order/)  

```sql
select name as Customers
from customers c
where id not in (select customerId from orders)
```

### [196. Delete Duplicate Emails](https://leetcode.com/problems/delete-duplicate-emails/)  

```sql
delete p1 from person p1, person p2
where p1.email = p2.email and p2.id < p1.id
```

### [197. Rising Temperature](https://leetcode.com/problems/rising-temperature/)  

```sql
select id from (
  select
    *,
    lag(temperature) over(order by recordDate) as prev_t,
    lag(recordDate) over(order by recordDate) as prev_dt
  from Weather
) foo
where temperature > prev_t 
  and datediff(recordDate, prev_dt) = 1
```

### [511. Game Play Analysis I](https://leetcode.com/problems/game-play-analysis-i/description/)  

```sql
select
  player_id,
  min(event_date) as first_login
from activity
group by player_id
```

### [577. Employee Bonus](https://leetcode.com/problems/employee-bonus/description/)  

```sql
select
  name,
  bonus
from Employee e
  left join Bonus b on e.empId = b.empId
where coalesce(bonus, 0) < 1000
```

### [584. Find Customer Referee](https://leetcode.com/problems/find-customer-referee/)  

```sql
select
  name
from customer c
where not exists (select name from customer where referee_id = 2 and customer.id = c.id)
```

### [586. Customer Placing the Largest Number of Orders](https://leetcode.com/problems/customer-placing-the-largest-number-of-orders/)  

```sql
with t as (
	select 
	  customer_number,
	  count(order_number) as cnt
	from orders
	group by customer_number
)
select customer_number from t
order by cnt desc
limit 1
```

### [595. Big Countries](https://leetcode.com/problems/big-countries/)  

```sql
select
  name,
  population,
  area
from world
where area >= 3000000 or population >= 25000000
```

### [596. Classes More Than 5 Students](https://leetcode.com/problems/classes-more-than-5-students/)  

```sql
select
  class
from courses
group by class
having count(1) >= 5
```

### [607. Sales Person](https://leetcode.com/problems/sales-person/)  

```sql
select name
from salesperson
where sales_id not in (select sales_id
					  from orders o 
					  join company c on o.com_id = c.com_id 
					  where name = 'RED')
```

### [607. Sales Person](https://leetcode.com/problems/sales-person/)  

```sql
select name
from salesperson
where sales_id not in (select sales_id
					  from orders o 
					  join company c on o.com_id = c.com_id 
					  where name = 'RED')
```

### [610. Triangle Judgement](https://leetcode.com/problems/triangle-judgement/description/)  

```sql
select *,
case when x + y > z and x + z > y and y + z > x then 'Yes' else 'No' end as triangle
from Triangle
```

### [619. Biggest Single Number](https://leetcode.com/problems/biggest-single-number/)  

```sql
select max(num) as num from (
  select
    num,
    count(num) over (partition by num) as cnt
  from MyNumbers
)t
where cnt = 1
```

### [620. Not Boring Movies](https://leetcode.com/problems/not-boring-movies/)  

```sql
select
  id,
  movie, 
  description,
  rating
from cinema
where id % 2 > 0
  and description <> "boring"
order by rating desc
```

### [627. Swap Salary](https://leetcode.com/problems/swap-salary/)  

```sql
update salary
set sex = case when sex = 'f' then  'm'
		  when sex = 'm' then 'f' end
```

### [1050. Actors and Directors Who Cooperated At Least Three Times](https://leetcode.com/problems/actors-and-directors-who-cooperated-at-least-three-times/)  

```sql
select
  actor_id, director_id
from actordirector
group by actor_id, director_id
having count(concat(actor_id, director_id)) >= 3
```

### [1068. Product Sales Analysis I](https://leetcode.com/problems/product-sales-analysis-i/)  

```sql
select
  product_name,
  year,
  price
from Product p
join Sales s
 on p.product_id = s.product_id
```

### [1075. Project Employees I](https://leetcode.com/problems/project-employees-i/)  

```sql
select 
  project_id, 
  round(avg(experience_years), 2) as average_years 
from (
	select
	  project_id, experience_years
	from Project p
	join Employee e
	 on p.employee_id = e.employee_id
)t
group by 1
```

### [1084. Sales Analysis III](https://leetcode.com/problems/sales-analysis-iii/)  

```sql
select
  product_id,
  product_name
from product
where exists (select product_id from sales where sale_date between '2019-01-01' and '2019-03-31' 
              and sales.product_id = product.product_id)
and not  exists (select product_id from sales where sale_date not between '2019-01-01' and '2019-03-31' 
              and sales.product_id = product.product_id)
```

### [1141. User Activity for the Past 30 Days I](https://leetcode.com/problems/user-activity-for-the-past-30-days-i/)  

```sql
select
  activity_date as day,
  count(distinct user_id) as active_users
from activity
where activity_date between '2019-07-27' - interval 29 day and '2019-07-27'
group by activity_date
having count(distinct user_id) >= 1
```

### [1148. Article Views I](https://leetcode.com/problems/article-views-i/)  

```sql
select
  distinct author_id as id
from views
where nullif(author_id, viewer_id) is null
order by author_id 
```

### [1179. Reformat Department Table](https://leetcode.com/problems/reformat-department-table/)  

```sql
select 
  id,
  max(case when month ='Jan' then revenue else null end) as jan_revenue,
  max(case when month ='Feb' then revenue else null end) as feb_revenue,
  max(case when month ='Mar' then revenue else null end) as mar_revenue,
  max(case when month ='Apr' then revenue else null end) as apr_revenue,
  max(case when month ='May' then revenue else null end) as may_revenue,
  max(case when month ='Jun' then revenue else null end) as jun_revenue,
  max(case when month ='Jul' then revenue else null end) as jul_revenue,
  max(case when month ='Aug' then revenue else null end) as aug_revenue,
  max(case when month ='Sep' then revenue else null end) as sep_revenue,
  max(case when month ='Oct' then revenue else null end) as oct_revenue,
  max(case when month ='Nov' then revenue else null end) as nov_revenue,
  max(case when month ='Dec' then revenue else null end) as dec_revenue
from department
group by id
order by id;
```

### [1211. Queries Quality and Percentage](https://leetcode.com/problems/queries-quality-and-percentage/)  

```sql
select
  query_name,
  round(avg(rating / position), 2) as quality,
  round(sum(case when rating < 3 then 1 else 0 end) / count(*) * 100, 2) as poor_query_percentage
from Queries
group by 1
```

### [1251. Average Selling Price](https://leetcode.com/problems/average-selling-price/)  

```sql
select 
  p.product_id,
  round(sum(units * price) / sum(units), 2) as average_price
from 
Prices p 
 join UnitsSold us on p.product_id = us.product_id
and purchase_date between start_date and end_date
group by 1
```

### [1280. Students and Examinations](https://leetcode.com/problems/students-and-examinations/)  

```sql
select 
  st.student_id,
  st.student_name,
  su.subject_name,
  coalesce( (select count(*) as cnt from Examinations e
			where st.student_id = e.student_id and su.subject_name = e.subject_name
			group by student_id, subject_name), 0) as attended_exams
from Students st, Subjects su
group by st.student_id, st.student_name, su.subject_name
order by st.student_id,  su.subject_name;
```

### [1327. List the Products Ordered in a Period](https://leetcode.com/problems/list-the-products-ordered-in-a-period/description/)  

```sql
select 
  product_name,
  sum(unit) as unit
from Products p
  left join Orders o on p.product_id = o.product_id
where dateadd(day, 1, eomonth(order_date, -1)) = '2020-02-01'
group by product_name
having sum(unit) >= 100
```

### [1378. Replace Employee ID With The Unique Identifier](https://leetcode.com/problems/replace-employee-id-with-the-unique-identifier/)  

```sql
select
  unique_id,
  name
from Employees e
left join EmployeeUNI eu on e.id = eu.id
```

### [1407. Top Travellers](https://leetcode.com/problems/top-travellers/)  

```sql
select
  name,
  coalesce(sum(distance), 0) as travelled_distance
from users u
 left join rides r on u.id = r.user_id
group by u.name, u.id
order by travelled_distance desc, name
```

### [1517. Find Users With Valid E-Mails](https://leetcode.com/problems/find-users-with-valid-e-mails/)  

```sql
select *
from Users
where mail LIKE '%@leetcode.com' AND
      mail regexp '^[a-zA-Z][a-zA-Z0-9_.-]*@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
```

### [1527. Patients With a Condition](https://leetcode.com/problems/patients-with-a-condition/)  

```sql
select *
from patients
where conditions like '% DIAB1%' or conditions like 'DIAB1%'
```

### [1581. Customer Who Visited but Did Not Make Any Transactions](https://leetcode.com/problems/customer-who-visited-but-did-not-make-any-transactions/)  

```sql
select
  ustomer_id,
  count(*) as count_no_trans
from visits v
where not exists (select visit_id from transactions where transactions.visit_id = v.visit_id)
group by customer_id
```

### [1587. Bank Account Summary II](https://leetcode.com/problems/bank-account-summary-ii/)  

```sql
with balance as (
	select 
	  account,
	  sum(amount) as balance
	from transactions
	group by account
	having sum(amount) > 10000
) 
select name, balance 
from users u, balance b 
where u.account = b.account
```

### [1633. Percentage of Users Attended a Contest](https://leetcode.com/problems/percentage-of-users-attended-a-contest/)  

```sql
select
  contest_id,
  ound(100.0 * count(contest_id) / (select count(*) from Users), 2) as percentage
from Register 
group by contest_id
order by percentage desc, contest_id
```

### [1661. Average Time of Process per Machine](https://leetcode.com/problems/average-time-of-process-per-machine/)  

```sql
select 
  machine_id,
  round( (sum(case when activity_type = 'end' then timestamp else 0 end) - 
   sum(case when activity_type = 'start' then timestamp else 0 end) ) /
    count(distinct process_id), 3) as processing_time
from Activity
group by machine_id
```

### [1667. Fix Names in a Table](https://leetcode.com/problems/fix-names-in-a-table/)  

```sql
select
  user_id,
  concat( upper(left(name, 1)), lower(substr(name, 2)) ) as name
from users
order by user_id
```

### [1683. Invalid Tweets](https://leetcode.com/problems/invalid-tweets/)  

```sql
select tweet_id from Tweets where length(content) > 15
```

### [1693. Daily Leads and Partners](https://leetcode.com/problems/daily-leads-and-partners/description/)  

```sql
select
  date_id,
  make_name,
  count(distinct lead_id) as unique_leads,
  count(distinct partner_id) as unique_partners
from DailySales
group by 1, 2
```

### [1729. Find Followers Count](https://leetcode.com/problems/find-followers-count/description/)  

```sql
select
  user_id,
  count(follower_id) as followers_count
from followers
group by 1
order by 1
```

### [1731. The Number of Employees Which Report to Each Employee](https://leetcode.com/problems/the-number-of-employees-which-report-to-each-employee/)  

```sql
select
  t1.employee_id,
  t1.name,
  count(*) as reports_count,
  round(avg(t2.age)) as average_age
from Employees t1
join Employees t2 on t1.employee_id = t2.reports_to
group by 1, 2
order by 1
```

### [1741. Find Total Time Spent by Each Employee](https://leetcode.com/problems/find-total-time-spent-by-each-employee/)  

```sql
select
  event_day as day,
  emp_id,
  sum(out_time - in_time) as total_time
from employees
group by 1, 2
```

### [1757. Recyclable and Low Fat Products](https://leetcode.com/problems/recyclable-and-low-fat-products/)  

```sql
select 
  product_id
from products
where low_fats = 'Y' and recyclable = 'Y'
```

### [1789. Primary Department for Each Employee](https://leetcode.com/problems/primary-department-for-each-employee/description/)  

```sql
select employee_id, department_id from (
	select 
	  employee_id,
	  department_id,
	  primary_flag,
	  count(*) over(partition by employee_id) as cnt
from Employee )t
where cnt = 1 or (cnt > 1 and primary_flag = 'Y')
```

### [1795. Rearrange Products Table](https://leetcode.com/problems/rearrange-products-table/)  

```sql
select product_id, 'store1' as store, store1 as price from products where store1 is not null
 union 
select product_id, 'store2' as store, store2 as price from products where store2 is not null
 union 
select product_id, 'store3' as store, store3 as price from products where store3 is not null
order by 1
```

### [1873. Calculate Special Bonus](https://leetcode.com/problems/calculate-special-bonus/)  

```sql
select
  employee_id,
  case when employee_id % 2 <> 0 and name not like 'M%' then salary else 0 end as bonus
from employees
order by 1
```

### [1890. The Latest Login in 2020](https://leetcode.com/problems/the-latest-login-in-2020/)  

```sql
select
  user_id,
  max(time_stamp) as last_stamp
from logins
where year(time_stamp) = 2020
group by 1
```

### [1965. Employees With Missing Information](https://leetcode.com/problems/employees-with-missing-information/)  

```sql
(select employee_id from Employees
 union
select employee_id from  Salaries)
 except
(select employee_id from Employees
 intersect
select employee_id from  Salaries)
order by employee_id
```

### [1978. Employees Whose Manager Left the Company](https://leetcode.com/problems/employees-whose-manager-left-the-company/)  

```sql
select
  employee_id
from Employees
where salary < 30000
 and manager_id not in (select employee_id from Employees)
order by 1
```

### [2356. Number of Unique Subjects Taught by Each Teacher](https://leetcode.com/problems/number-of-unique-subjects-taught-by-each-teacher/)  

```sql
select 
  teacher_id,
  count(distinct subject_id) as cnt
from Teacher
group by 1
```
### [185. Department Top Three Salaries](https://leetcode.com/problems/department-top-three-salaries/)  

```sql
with t as (
  select
    departmentId,
    name,
    salary,
    dense_rank() over(partition by departmentId order by salary desc) as rnk
  from employee
)
select 
  d.name as Department,
  t.name as Employee,
  salary as Salary
from t join department d on t.departmentId = d.id
where rnk <= 3
order by Department, Salary desc
```

### [262. Trips and Users](https://leetcode.com/problems/trips-and-users/)  

```sql
select
  request_at as Day,
  round(sum(case when left(status, 9) = 'cancelled' then 1 else 0 end) /
    count(*), 2) as "Cancellation Rate"
from Trips t
where not exists (select users_id from Users u where banned = 'Yes'
                  and role = 'client' and u.users_id = t.client_id)
and not exists (select users_id from Users u where banned = 'Yes'
                and role = 'driver' and u.users_id = t.driver_id)
and request_at between '2013-10-01' and '2013-10-03'
group by 1
```

### [601. Human Traffic of Stadium](https://leetcode.com/problems/human-traffic-of-stadium/)  

```sql
select id, visit_date, people
from 
  (select id, visit_date, people, 
          id - row_number() over (order by visit_date) as rn
  from Stadium
  where people >= 100) t
join
  (select rn, count(*) as cnt
  from 
    (select id, people, 
             id - row_number() over (order by visit_date) as rn
    from Stadium
    where people >= 100) foo
  group by rn
  having count(*) >= 3) t1
on t.rn = t1.rn
order by visit_date
```
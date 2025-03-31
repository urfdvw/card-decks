# how to write more readable queries

-   case does not matter
-   `select` always with table name and rename with `as`
-   indent all parallel column names
-   indent `on` as it is part of `join`
-   in condition, mention the most relevant first

```sql
-- example of above
-- https://leetcode.com/problems/combine-two-tables/
select
    Person.firstName as firstName,
    Person.lastName as lastName,
    Address.city as city,
    Address.state as state
from Person
left join Address
    on Address.personId = Person.personId
```

-   logics like `and` indent with `on` as no need for another layer
-   when the logic is clear, instead of using `join ... where`, use `join .. and`
    -   but not with `left join`

```sql
-- example of above
-- https://leetcode.com/problems/employees-earning-more-than-their-managers/
select Member.name as Employee
from Employee as Member
join Employee as Lead
    on Lead.id = Member.managerId
    and Lead.salary < Member.salary
```

```sql
-- https://leetcode.com/problems/employee-bonus/
select
    Employee.name as name,
    Bonus.bonus as bonus
from Employee
left join Bonus
    on Bonus.empId = Employee.empId
where Bonus.bonus < 1000
    or Bonus.bonus is null
```

---

# order of MS SQL execution

-   `FROM`
-   `JOIN`（如果有）
-   `WHERE`
-   `GROUP BY`
-   `HAVING`
-   `SELECT`
-   `ORDER BY`
-   `LIMIT / OFFSET`（可选）

---

# using `select left ... is null`

> trick

```sql
-- example of above
-- https://leetcode.com/problems/customers-who-never-order/
select distinct Customers.name as Customers
from Customers
left join Orders
    on Orders.customerId = Customers.id
where Orders.customerId is null
```

---

# where can `count` be used

-   `HAVING`
-   `SELECT`
-   `ORDER BY`

```sql
-- example of above
-- https://leetcode.com/problems/duplicate-emails
select Person.email
from Person
group by Person.email
having count(Person.id) > 1
```

---

# `UNIQUE` vs `DISTINCT`

| 功能       | Oracle              | SQL Server               |
| ---------- | ------------------- | ------------------------ |
| 查询唯一值 | `SELECT UNIQUE ...` | `SELECT DISTINCT ...` ✅ |
| 列唯一约束 | `UNIQUE`            | `UNIQUE` ✅              |

---

# about `NULL`

## ✅ 基本语法

| 用法                 | 说明                  |
| -------------------- | --------------------- |
| `column IS NULL`     | 检查某列是否为 NULL   |
| `column IS NOT NULL` | 检查某列是否不为 NULL |

## 🔧 常用函数（SQL Server 特有的/标准 SQL）

### 1. `ISNULL(expr, replacement)` ✅（SQL Server 专属）

如果 `expr` 为 `NULL`，就用 `replacement` 替代。

```sql
SELECT ISNULL(nickname, 'No Nickname') AS display_name
FROM Users;
```

### 2. `COALESCE(expr1, expr2, ..., exprN)` ✅（跨数据库标准）

返回第一个非 `NULL` 的值，功能上类似 `ISNULL`，但更通用。

```sql
SELECT COALESCE(nickname, username, 'Unknown') AS name_to_show
FROM Users;
```

### 3. `NULLIF(expr1, expr2)` ✅（很有用！）

如果两个值相等，返回 `NULL`；否则返回第一个值。

```sql
SELECT NULLIF(score, 0) AS adjusted_score
FROM TestResults;
-- 如果 score 是 0，就变成 NULL（避免除以 0 等情况）
```

## ❗ 注意事项：`NULL` 与比较运算

你不能直接用 `= NULL` 或 `<> NULL`！

```sql
SELECT * FROM Person WHERE name = NULL;     -- ❌ 错误
SELECT * FROM Person WHERE name IS NULL;    -- ✅ 正确
```

```sql
-- https://leetcode.com/problems/find-customer-referee
select name
from Customer
where referee_id is null
    or referee_id <> 2
```

## 🔍 进阶使用场景

### 1. 排序时将 NULL 放后

```sql
ORDER BY column_name ASC NULLS LAST
-- SQL Server 不直接支持这个语法，可以模拟：
ORDER BY CASE WHEN column_name IS NULL THEN 1 ELSE 0 END, column_name
```

### 2. 过滤 null-aware 聚合（如 count）

```sql
SELECT COUNT(salary) FROM Employee;  -- 不包括 NULL
SELECT COUNT(*) FROM Employee;       -- 包括所有行
```

> cross join

```sql
-- https://leetcode.com/problems/students-and-examinations/
-- TODO：还不是很熟
select
    Students.student_id as student_id, -- Students not Examinations: use left of left join
    Students.student_name as student_name,
    Subjects.subject_name as subject_name,
    count(Examinations.student_id) as attended_exams -- count non-null
from Students
cross join Subjects
left join Examinations
    on Examinations.student_id = Students.student_id
    and Examinations.subject_name = Subjects.subject_name
group by
    Students.student_id, -- left of left join
    Students.student_name,
    Subjects.subject_name
order by student_id, subject_name
```

## ✅ 总结：关于 NULL 的操作不止 `IS NULL` 和 `IS NOT NULL`，你可以用：

| 功能            | 示例                                  |
| --------------- | ------------------------------------- |
| 检查是否为 null | `IS NULL` / `IS NOT NULL`             |
| 替换 null       | `ISNULL(x, val)` / `COALESCE(x, val)` |
| 相等则返回 null | `NULLIF(x, y)`                        |
| 聚合过滤 null   | `COUNT(column)` 不计 null             |

---

# crud on CTEs

| 操作                | 是否影响原表                   |
| ------------------- | ------------------------------ |
| `SELECT * FROM cte` | ❌ 不影响                      |
| `DELETE FROM cte`   | ✅ 会删除 CTE 引用的原始表数据 |
| `UPDATE cte`        | ✅ 会修改原始表数据            |
| `INSERT INTO cte`   | ❌ 不允许（CTE 不能插入）      |

判断可更新的 CTE 的主表的两大要点：

| 条件                               | 判断依据                                         | 举例                                                                       |
| ---------------------------------- | ------------------------------------------------ | -------------------------------------------------------------------------- |
| ✅ 可更新性                        | 所有返回的行，必须能对应到某个**基础表中的一行** | `SELECT p.Id FROM Person p` 可以删除 Person                                |
| ❌ 含多个基础表                    | SQL Server 无法确认到底删除哪个表                | `SELECT * FROM Person p JOIN Department d` 就不行                          |
| ❌ 有聚合、DISTINCT、GROUP BY、TOP | 聚合结果无法映射回原始行                         | `SELECT DepartmentId, COUNT(*) FROM Person GROUP BY DepartmentId` 不可更新 |
| ❌ 派生列、函数生成列              | 比如 `LEN(p.Name)`，不是实际表字段               | 删除不明确，SQL Server 不允许                                              |

```sql
-- https://leetcode.com/problems/delete-duplicate-emails/
/* Write your T-SQL query statement below */
with OrderedPerson as (
    select
        id,
        email,
        ROW_NUMBER() over(
            partition by email
            order by id
        ) as rownumber
    from Person
)
delete from OrderedPerson
where rownumber > 1
```

---

# window aggregation

```sql
<aggregation function>() OVER (
    PARTITION BY ...
    ORDER BY ...
    ROWS BETWEEN  ...
)
```

example

```sql
--- row number
SELECT
    Id,
    Email,
    ROW_NUMBER() OVER (PARTITION BY Email ORDER BY Id) AS rn
FROM Person;
```

-   [196](https://leetcode.com/problems/delete-duplicate-emails/)

```sql
--- sum
SELECT
    Name,
    Department,
    Year,
    Salary,
    SUM(Salary) OVER (
        PARTITION BY Department, Year
    ) AS DeptYearTotal
FROM Employee;
```

---

# How to find the date of yesterday

`dateadd(day, -1, Today.recordDate)`

```sql
-- https://leetcode.com/problems/rising-temperature/
select
    Today.id as id
from Weather as Today
join Weather as Yesterday
    on Yesterday.recordDate = dateadd(day, -1, Today.recordDate)
    and Yesterday.temperature < Today.temperature
```

---

# about `group by`

table columns are either in group by or select

```sql
-- https://leetcode.com/problems/customer-who-visited-but-did-not-make-any-transactions/?envType=study-plan-v2&envId=top-sql-50

select
    Visits.customer_id as customer_id,
    count(Visits.visit_id) as count_no_trans
from Visits
left join Transactions
    on Transactions.visit_id = Visits.visit_id
where transaction_id is null
group by Visits.customer_id
```

<!-- other leetcode examples -->

---

# 1661

> cte, average, avg, round

```sql
-- https://leetcode.com/problems/average-time-of-process-per-machine/
with Duration as (
    select
        ProcessStart.machine_id as machine_id,
        ProcessEnd.timestamp - ProcessStart.timestamp as length
    from Activity as ProcessStart
    join Activity as ProcessEnd
        on ProcessEnd.machine_id = ProcessStart.machine_id
        and ProcessEnd.process_id = ProcessStart.process_id
        and ProcessStart.activity_type = 'start'
        and ProcessEnd.activity_type = 'end'
)
select
    Duration.machine_id as machine_id,
    round(avg(Duration.length), 3) as processing_time
from Duration
group by Duration.machine_id
```

---

# 570

> cte, null, count

```sql
-- https://leetcode.com/problems/managers-with-at-least-5-direct-reports/
with Relation as (
    select
        Lead.name as leadName,
        Lead.id as leadId,
        Member.name as memberName
    from Employee as Lead
    join Employee as Member
        on Member.managerId = Lead.id
)
select
    leadName as name
from Relation
group by leadId, leadName
having count(1) >= 5

/**** missed case ****
- leads have same name but different ids
    - include id in CTE
- | 101 | null | A          | null      | ??? WTF is this case ???
    - instead of counting name, count 1 to include null as a case
*/
```

---

# 1934

> rate, iff for conversion, round, left join, coalesce

```sql
-- https://leetcode.com/problems/confirmation-rate/
with EventRate as (
    select
        user_id as user_id,
        round(sum(iif(action = 'confirmed', 1.0, 0.0)) / count(1), 2)
            -- 1.0 to ensure float
            as confirmation_rate
    from Confirmations
    group by user_id
)
select
    Signups.user_id as user_id,
    coalesce(EventRate.confirmation_rate, 0) as confirmation_rate
from Signups
left join EventRate
    on EventRate.user_id = Signups.user_id

-- don't forget to round
```

---

# 1251
> date time, datetime, between, with, coalesce

```sql
-- https://leetcode.com/problems/average-selling-price
with SoldPrice as (
    select
        UnitsSold.product_id,
        Prices.price,
        UnitsSold.units
    from UnitsSold
    join Prices
        on Prices.product_id = UnitsSold.product_id
        and UnitsSold.purchase_date 
            between Prices.start_date 
            and Prices.end_date
),
AveragePrice as (
    select
        SoldPrice.product_id,
        round(
            sum(cast(SoldPrice.price as float) * SoldPrice.units) / sum(SoldPrice.units),
            2
        )
            as average_price
    from SoldPrice
    group by product_id
),
Products as (
    select distinct
        product_id
    from Prices
)
select
    Products.product_id,
    coalesce(AveragePrice.average_price, 0.0) as average_price
from Products
left join AveragePrice
    on AveragePrice.product_id = Products.product_id

-- edge case, what if a product never sold? show as 0
-- only one `with` needed for a chain of cte
```

---
# 1075
> float, decimal

```sql
-- https://leetcode.com/problems/project-employees-i
with ProjectExperience as (
    select
        Project.project_id as project_id,
        Employee.experience_years as experience_years
    from Project
    join Employee
        on Employee.employee_id = Project.employee_id
)
select
    ProjectExperience.project_id as project_id,
    round(avg(1.0 * ProjectExperience.experience_years), 2) as average_years
from ProjectExperience
group by ProjectExperience.project_id
order by ProjectExperience.project_id
-- AVG will not auto cast to float
-- don't forget round
-- order?
-- WTF? cast to float is different from 1.0 *
```

```sql
-- shorter
select
    Project.project_id as project_id,
    round(avg(1.0 * Employee.experience_years), 2) as average_years
from Project
join Employee
    on Employee.employee_id = Project.employee_id
group by Project.project_id
order by Project.project_id
```
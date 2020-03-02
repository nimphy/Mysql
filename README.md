# Mysql
---
### 1， Leetcode175. 组合两个表  
+ 题意：给定来个表，组合。并且第二个可以为空。  
+ 思路：left join。  
```
Select FirstName,Lastname,City,State
From Person left join Address
on Person.PersonId=Address.PersonId
```
---
### 2， Leetcode176. 第二高的薪水   
+ 题意：给定工资表，输出第二高工资，不存在输出null。  
+ 思路：降序排序，输出第二高的limit x,y表示过滤x条，取y条。 这里是limit 1,1。 然后是ifnull(x,y)函数，可以在x为假的时候返回y，这里y为null。  
```
select ifnull
(
    (select Salary
        from Employee 
        group by Salary
        order by Salary desc
        limit 1,1),
    null
) 
as SecondHighestSalary
```
---
### 3， Leetcode177. 第N高的薪水
+ 和上题一样
```
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
   set N=N-1;
   RETURN ( 
      select ifnull
(
    (select Salary
        from Employee 
        group by Salary
        order by Salary desc
        limit N,1),
    null
) 
as getNthHighestSalary
  );
END
```
---
### 4， Leetcode178. 分数排名  
+ 题意：按分数降序排名，排名是连续的。  
+ 思路：rank的时候看有几个比distinct自己小。  
```
select p1.Score as Score,(
    select count(distinct p2.Score)
    from Scores p2
    where p2.score>=p1.score
) as Rank
from Scores p1
order by Score desc
```
----
### 6， Leetcode181. 超过经理收入的员工  
+ 题意：给定工资，直系上司，输出哪些人的工资比上级高。  
+ 思路：连接两张表即可。  
```
select a.Name as Employee
from Employee a left join Employee  b
on a.ManagerId=b.Id 
where a.salary>b.salary
```
---
### 7， Leetcode182. 查找重复的电子邮箱  
+ 题意：给定使用的邮箱，输出带重复的。  
+ 思路：连接两张表，输出有重复的，去重。  
```
select  distinct a.Email
from Person a,Person b
where a.Email=b.email and a.id!=b.id
```
---
### 8， Leetcode183. 从不订购的客户 
+ 题意：给定注册用户和消费表，输出重来没有消费过的。  
+ 思路：可以用not exist，也可以用not in()。  
```
select a.name as Customers
from Customers a
where a.Id not in (
    select CustomerID 
    from Orders
)
```
---
### 9， Leetcode184. 部门工资最高的员工  
+ 题意：给定员工表，输出给个部门最高的员工、工资。  
+ 思路：按照部门排序，如果工资和max，则输出。
+ 技巧，in()，可以有多个参数。  
```
select b.Name as Department,a.Name as Employee,Salary
from Employee a,Department b
where a.DepartmentId=b.Id and (
    (DepartmentId,Salary) in 
    (
        select DepartmentId,max(Salary)
        from Employee
        group by DepartmentId
    )
)
```
---
### 10， Leetcode185. 部门工资前三高的所有员工  
+ 题意：给定工资表和部门表，求每个部门前三高的工资，按照部门标号、工资降序输出。  
+ 思路：如果满足，是同一个部门，而且比自己高的数字不超过2个，那么则满足。 排序放在最后面。  
```
select a.Name as Department,b.Name as Employee, b.Salary as Salary
from Employee as b left join Department as a
on a.Id=b.DepartmentId
where b.id in
(
    select w.id 
    from Employee as w left join Employee as v
    on w.DepartmentId=v.DepartmentId and w.Salary<v.Salary
    group by w.Id 
    having count(distinct v.Salary)<=2
) and b.DepartmentId in (select Id from Department)
order by a.Id asc,b.Salary desc
```



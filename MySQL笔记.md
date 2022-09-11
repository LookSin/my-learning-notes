# 0.数据库的四个基本概念

## 1.数据

**描述事物的符号记录。**

数据的含义成为数据的语义，数据与其语义是不可分的。

## 2.数据库

数据库是长期存储在计算机内的、有组织的、可共享的大量数据的**集合**。数据库中的数据按一定的数据模型组织、描述和存储，具有较小的冗余度、较高的数据独立性和易扩展性，并可为各种用户共享。

三个特点：永久存储、有组织、可共享。

## 3.数据库管理系统

是一种操作和管理数据库的大型软件，用于建立、使用和维护数据库，对数据库进行统一管理和控制，用户通过数据库管理系统访问数据库中表内的数据。

## 4.数据库系统

数据库系统是由数据库、数据库管理系统(及其应用开发工具)、应用程序和数据库管理员组成的存储、管理、处理和维护数据的系统。简称为数据库。

# 1.基本的SELECT语句

## 1.1 SQL分类

- DDL，数据定义语言。主要关键字包括CREATE、DROP、ALTER等。
- DML，数据操作语言。主要关键字包括INSERT、DELETE、UPDATE、**SELECT**(重中之重)等。
- DCL，数据控制语言。主要关键字包括GRANT、REVOKE、COMMIT、ROLLBACK、SAVEPOINT等。

因为查询语句使用的非常的频繁，所以很多人把查询语句单拎出来一类：DQL(数据查询语言)。还有单独将COMMIT、ROLLBACK取出来称为TCL(事务控制语言)。

## 1.2 SQL的规则与规范

### 1.2.1 基本规则

- SQL可以写在一行或多行。为了提高可读性，各子句分行写，必要时使用缩进
- 每条命令以;或\g或\G结束
- 关键字不能被缩写也不能分行
- 关于标点符号
  - 必须保证所有的()、单引号、双引号是成对结束的
  - 必须使用英文状态下的半角输入方式
  - 字符串型和日期时间类型的数据可以使用单引号表示
  - 列的别名，尽量使用双引号，而且不建议省略as

### 1.2.2 SQL大小写规范(推荐)

- 数据库名、表名、表别名、字段名、字段别名等都小写
- SQL关键字、函数名、绑定变量等都大写

### 1.2.3 注释

```mysql
#这是单行注释，MySQL特有的方式
-- 这也是单行注释，--后面必须包含一个空格
/* 这是多行注释 */
```

## 1.3 导入

导入现有的数据表、表的数据。

- 方式1：source 文件的全路径名
- 方式2：基于具体的图形化界面的工具可以导入数据。比如SQLyog中选择"工具"——"执行SQL脚本"

## 1.4 查询常数

SELECT查询还可以对常数进行查询，就是在SELECT查询结果中增加一列固定的常数列。这列取值是我们指定的，而不是从数据表中动态取出的。

**目的：**如果我们想整合不同的数据源，用常数列作为这个表的标记，就需要查询常数。

比如说，我们想对employees数据表中的员工姓名进行查询，同时增加一列字段`corporation`,这个字段固定值为"侯冰芷"，可以这样写：

```sql
SELECT '侯冰芷' as corporation,last_name FROM employees;
```

## 1.5 显示表结构

```sql
DESCRIBE employees; #显示了表中字段的详细信息
```

## 1.6 用WHERE过滤数据

**WHERE紧跟在FROM后面。**

```sql
#查询90号部门的员工信息
SELECT *
FROM employees
#过滤条件
WHERE department_id = 90;
```

## 1.7 练习题

### 1.7.1 计算全年的工资

```sql
SELECT employee_id,last_name,salary * 12 *(1+IFNULL(commission_pct,0)) "ANNUAL SALARY"
FROM employees;
```

### 1.7.2 查询去重后的数据

```sql
SELECT DISTINCT job_id
FROM employees;
```

### 1.7.3 查询特定条件

```sql
#查询工资大于12000的员工的姓名和工资
SELECT last_name,salary
FROM employees
WHERE salary > 12000;
```

### 1.7.4 显示表结构并查询数据

```sql
DESCRIBE departments;

SELECT * FROM departments;
```

# 2.运算符

## 2.1 算术运算符

```mysql
#+, -, *, / DIV, % mod
SELECT 100, 100 + 0,100 - 0.5
FROM DUAL;#DUAL伪表，在SQL中， +没有连接的作用，就表示加法运算
```

相除时，如果分母为0，则为NULL。

取模运算时，结果的正负 与符号前面的被模数有关，与符号后面的模数无关。

```mysql
#查询员工id为偶数的员工信息
SELECT employee_id,last_name,salary
FROM employees
WHERE employee_id % 2 = 0;
```

## 2.2 比较运算符

字符串存在隐式转换。如果转换数值不成功，则看作0。

```mysql
SELECT 1 = 'a',0 = 'a'
FROM DUAL;
#结果分别为0，1
```

两边都是字符串的话，按照ANSI的比较规则进行比较。

只要有NULL参与运算，结果就为NULL。但是**安全等于运算符(<=>)为NULL而生**。

```mysql
SELECT NULL <=> NULL,1 <=> NULL
FROM DUAL;
#结果分别为1，0

SELECT last_name,salary,commission_pct
FROM employees
WHERE commission_pct <=> NULL;
#查询表中commission_pct为NULL的数据有哪些
```

## 2.3 逻辑运算符

- OR  ||
- AND &&
- NOT !
- XOR

```mysql
SELECT last_name,salary,department_id
FROM employees
WHERE department_id=10 OR department_id=20;
#OR表示满足一个条件即可，AND表示条件要同时满足
```

```mysql
SELECT last_name,salary,department_id
FROM employees
WHERE department_id = 50 XOR salary > 6000;
#XOR异或运算符，追求"异"，前后一个条件满足，一个条件不满足
```

OR和AND可以一起使用，但是AND的优先级高于OR的优先级。

()优先级最高。

## 2.4 练习

```mysql
SELECT last_name,salary
FROM employees
WHERE salary NOT BETWEEN 5000 AND 12000;
#选择工资不在5000和12000的员工的姓名和工资

SELECT last_name,department_id
FROM employees
WHERE department_id IN (20,50);
#选择在20或50号部门工作的员工姓名和部门号

SELECT last_name,job_id
FROM employees
WHERE manager_id IS NULL;
#选择公司中没有管理者的员工的姓名及job_id

SELECT last_name,salary,commission_pct
FROM employees
WHERE commission_pct IS  NOT NULL;
#选择公司中有奖金的员工的姓名、工资和奖金级别

SELECT last_name
FROM employees
WHERE last_name LIKE '__a%';
#选择员工姓名第三个字母是a的员工姓名

SELECT last_name
FROM employees
WHERE last_name LIKE '%a%' AND last_name LIKE '%k%';
#选择姓名中有字母a和k的员工姓名
```

# 3.排序与分页

## 3.1 ORDER BY实现排序操作

- 升序：ASC(ascend)
- 降序：DESC(descend)

```mysql
SELECT employee_id,last_name,salary
FROM employees
ORDER BY salary DESC;
#按照salary从高到低的顺序显示员工信息
```

如果在ORDER BY后没有显示指明排序的方式的话，则默认按照升序排列。

```mysql
SELECT employee_id,salary,salary * 12 annual_sal
FROM employees
ORDER BY annual_sal;
#我们可以使用列的别名，进行排序
#列的别名只能在ORDER BY中使用，不能在WHERE中使用
```

强调格式：WHERE需要声明在FROM后，ORDER BY之前。

```mysql
SELECT employee_id,salary
FROM employees
WHERE department_id IN (50,60,70)
ORDER BY department_id DESC;
```

**二级排序**

```mysql
SELECT employee_id,salary,department_id
FROM employees
ORDER BY department_id DESC,salary ASC;
#显示员工信息，按照department_id的降序排列，salary的升序排列
```

## 3.2 LIMIT实现分页操作

```mysql
SELECT employee_id,last_name
FROM employees
LIMIT 0,20
#每页显示20条记录，偏移量为0，此时显示第一页

SELECT employee_id,last_name
FROM employees
LIMIT 20,20
#每页显示20条记录，此时显示第二页
```

WHERE...ORDER BY...LIMIT声明顺序如下

```mysql
SELECT employee_id,last_name,salary
FROM employees
WHERE salary > 6000
ORDER BY salary DESC
LIMIT 0,10;
```

## 3.3 练习

```mysql
SELECT last_name,department_id,salary * 12 annual_salary
FROM employees
ORDER BY annual_salary DESC,last_name ASC;
#查询员工的姓名，部门号和年薪，按年薪降序，按姓名升序显示
```

```mysql
SELECT last_name,salary 
FROM employees
WHERE salary NOT BETWEEN 8000 AND 17000
ORDER BY salary DESC
LIMIT 20,20;
#选择工资不在 8000到 17000的员工的姓名和工资，按工资降序，显示第21到40位置的数据
```

```mysql
SELECT employee_id,last_name,department_id
FROM employees
WHERE email LIKE '%e%'
ORDER BY LENGTH(email) DESC,department_id;
#查询邮箱中包含e的员工信息，并先按邮箱的字节数排序，再按部门号升序
```

# 4.多表查询(重要)

```mysql
#多表查询的正确方式:需要有连接条件
SELECT employee_id,department_name
FROM employees,departments
WHERE employees.department_id = departments.department_id;
#两个表的连接条件
```

```mysql
#如果查询语句中出现了多个表中都存在的字段，则必须指明此字段所在的表
SELECT employees.employee_id,departments.department_name,employees.`department_id`
FROM employees,departments
WHERE employees.department_id = departments.department_id;
#建议:从sql优化的角度，建议多表查询时，每个字段前都指明其所在的表
```

```mysql
#可以给表起别名,在SELECT和WHERE中使用表的别名
SELECT emp.employee_id,dept.department_name,emp.`department_id`
FROM employees emp,departments dept
WHERE emp.department_id = dept.department_id;
```

**如果给表起了别名，一旦在SELECT和WHERE中使用别名的话，则必须使用表的别名，而不能再使用表的原名。**

如果有n个表实现多表的查询，则至少需要n-1个连接条件。

## 4.1 等值连接 VS 非等值连接

```mysql
#非等值连接
SELECT e.last_name,e.salary,j.grade_level
FROM employees e,job_grades j
WHERE e.`salary`BETWEEN j.`lowest_sal`AND j.`highest_sal`;
```

## 4.2 自连接 VS 非自连接

```mysql
#自连接
#练习:查询员工id，员工姓名及其管理者的id和姓名
SELECT emp.employee_id,emp.`last_name`,mgr.`employee_id`,mgr.`last_name`
FROM employees emp,employees mgr
WHERE emp.`manager_id`= mgr.`manager_id`;
```

## 4.3 内连接和外连接

1. 内连接：合并具有同一列的两个以上的表的行，结果集中不包含一个表与另一个表不匹配的行。
2. 外连接：合并具有同一列的两个以上的表的行，结果集中**除了**包含一个表与另一个表不匹配的行之外，还查询到了左表 或 右表中不匹配的行。

​		外连接的分类：左外连接、右外连接和满外连接。

```mysql
#SQL99语法实现内连接
SELECT last_name,department_name
FROM employees e JOIN departments d
ON e.`department_id`= d.`department_id`;
```

```mysql
#SQL99语法实现左外连接
#练习：查询所有员工的last_name,department_name信息
SELECT last_name,department_name
FROM employees e LEFT OUTER JOIN departments d
ON e.`department_id`= d.`department_id`;
#右外连接
SELECT last_name,department_name
FROM employees e RIGHT OUTER JOIN departments d
ON e.`department_id`= d.`department_id`;
```

## 4.4 使用SQL99实现7种JOIN操作

**UNION 和 UNION ALL**的使用

UNION：会执行去重操作

UNION ALL：不会执行去重操作

结论：如果明确知道合并数据后的数据结果不存在重复数据，或者不需要去除重复的数据，则尽量使用UNION ALL语句，以提高数据查询的效率。

![image-20220807163854835](C:\Users\52908\AppData\Roaming\Typora\typora-user-images\image-20220807163854835.png)

```mysql
#中图：内连接
SELECT employee_id,department_name
FROM employees e JOIN departments d
ON e.`department_id`=d.`department_id`;

#左上图；左外连接
SELECT employee_id,department_name
FROM employees e LEFT JOIN departments d
ON e.`department_id`=d.`department_id`;

#右上图：右外连接
SELECT employee_id,department_name
FROM employees e RIGHT JOIN departments d
ON e.`department_id`=d.`department_id`;

#左中图：
SELECT employee_id,department_name
FROM employees e LEFT JOIN departments d
ON e.`department_id`=d.`department_id`
WHERE d.`department_id` IS NULL;

#右中图：
SELECT employee_id,department_name
FROM employees e RIGHT JOIN departments d
ON e.`department_id`=d.`department_id`;
WHERE e.department_id IS NULL;

#左下图：满外连接
#左上图 UNION ALL 右中图
SELECT employee_id,department_name
FROM employees e LEFT JOIN departments d
ON e.`department_id`=d.`department_id`
UNION ALL
SELECT employee_id,department_name
FROM employees e RIGHT JOIN departments d
ON e.`department_id`=d.`department_id`
WHERE e.department_id IS NULL;

#右下图
#左中图 UNION ALL 右中图
SELECT employee_id,department_name
FROM employees e LEFT JOIN departments d
ON e.`department_id`=d.`department_id`
WHERE d.`department_id` IS NULL
UNION ALL
SELECT employee_id,department_name
FROM employees e RIGHT JOIN departments d
ON e.`department_id`=d.`department_id`
WHERE e.department_id IS NULL;
```

## 4.5 练习

```mysql
#显示所有员工的姓名，部门号和部门连接
SELECT e.last_name,e.department_id,d.department_name
FROM employees e JOIN departments d
ON e.`department_id`=d.`department_id`;

#查询90号部门员工的job_id和90号部门的location_id
SELECT job_id,location_id
FROM employees e JOIN departments d
ON e.`department_id`=d.`department_id`
WHERE d.`department_id`=90;

#选择所有有奖金的员工的last_name,department_name,location_id,city
SELECT e.last_name,e.`commission_pct`,d.department_id,d.location_id,l.city
FROM employees e LEFT JOIN departments d
ON e.`department_id`=d.`department_id`
LEFT JOIN locations l
ON d.`location_id`=l.`location_id`
WHERE e.`commission_pct` IS NOT NULL;

#查询员工所在的部门名称、部门地址、姓名、工作、工资，其中员工所在部门的部门名称为'Executive'
SELECT d.department_name,l.street_address,e.last_name,e.job_id,e.salary,d.`department_id`
FROM employees e JOIN departments d
ON e.`department_id`=d.`department_id`
JOIN locations l
ON d.`location_id`=l.`location_id`
WHERE d.`department_name`='Executive';
```

![image-20220810155033603](C:\Users\52908\AppData\Roaming\Typora\typora-user-images\image-20220810155033603.png)

```mysql
SELECT emp.last_name "employees",emp.employee_id "emp#",mgr.last_name "manager",mgr.employee_id "Mgr#"
FROM employees emp LEFT JOIN employees mgr
ON emp.manager_id=mgr.employee_id;
```

```mysql
#查询哪些部门没有员工
SELECT d.department_id
FROM departments d LEFT JOIN employees e
ON d.`department_id`=e.`department_id`
WHERE e.`department_id` IS NULL;

#查询哪个城市没有部门
SELECT l.location_id,l.city
FROM locations l LEFT JOIN departments d
ON l.`location_id`=d.`location_id`
WHERE d.`location_id` IS NULL;

#查询部门名为 Sales 或 IT 的员工信息
SELECT e.employee_id,e.last_name,e.department_id
FROM employees e JOIN departments d
ON e.`department_id`=d.`department_id`
WHERE d.`department_name` IN ('Sales','IT');
```

# 5.单行函数

## 5.1 数值函数

```mysql
# 基本的操作
SELECT ABS(-123),ABS(32),SIGN(43),PI(),CEIL(32.32),CEILING(-43.23),FLOOR(32.32),
FLOOR(-43.23),MOD(12,5)
FROM DUAL;

# 取随机数0-1,若参数相同，则随机数相同
SELECT RAND(),RAND(),RAND(10),RAND(10),RAND(-1),RAND(-1)
FROM DUAL;

# 四舍五入,截断操作
SELECT ROUND(123.556),ROUND(123.456,0),ROUND(123.456,1),ROUND(123.456,-1)
FROM DUAL;
SELECT TRUNCATE(123.456,0),TRUNCATE(123.496,1),TRUNCATE(129.45,-1)
FROM DUAL;

# 单行函数可以嵌套
SELECT TRUNCATE(ROUND(123.456,2),0)
FROM DUAL;
```

## 5.2 字符串函数

```mysql
# LPAD:实现右对齐效果
# RPAD:实现左对齐效果
SELECT employee_id,last_name,LPAD(salary,10,' ')
FROM employees;
# CONCAT(s1,s2,......,sn)连接s1,s2,...sn为一个字符串
```

## 5.3 日期和时间函数

```mysql
# 获取日期、时间
SELECT CURDATE(),CURRENT_DATE(),CURTIME(),NOW(),SYSDATE(),UTC_DATE(),UTC_TIME()
FROM DUAL;

# 日期与时间戳的转换
SELECT UNIX_TIMESTAMP(),UNIX_TIMESTAMP('2021-10-01 12:12:32'),
FROM_UNIXTIME(1660622907),FROM_UNIXTIME(1633061552)
FROM DUAL;

# 获取月份、星期、星期数、天数等函数
SELECT YEAR(CURDATE()),MONTH(CURDATE()),DAY(CURDATE()),
HOUR(CURTIME()),MINUTE(NOW()),SECOND(SYSDATE())
FROM DUAL;

# 日期的操作函数
SELECT EXTRACT(SECOND FROM NOW()),EXTRACT(DAY FROM NOW()),
EXTRACT(HOUR_MINUTE FROM NOW()),EXTRACT(QUARTER FROM '2022-8-16')
FROM DUAL;

# 计算日期和时间的函数
SELECT NOW(),DATE_ADD(NOW(),INTERVAL 1 YEAR)
FROM DUAL;

SELECT DATE_ADD(NOW(),INTERVAL 1 DAY) AS col1,DATE_ADD('2022-8-16 17:03:57',INTERVAL 1
SECOND) AS col2,
ADDDATE('2022-8-16 17:03:57',INTERVAL 1 SECOND) AS col3,
DATE_ADD('2022-8-16 17:03:57',INTERVAL '1_1' MINUTE_SECOND) AS col4,
DATE_ADD(NOW(),INTERVAL -1 YEAR) AS col5,
DATE_ADD(NOW(),INTERVAL '1_1' YEAR_MONTH) AS col6
FROM DUAL;

SELECT ADDTIME(NOW(),20),SUBTIME(NOW(),30),SUBTIME(NOW(),'1:1:3'),DATEDIFF(NOW(),'2022-10-01'),
TIMEDIFF(NOW(),'2022-8-16 17:53:13'),FROM_DAYS(366),TO_DAYS('0000-12-25'),
LAST_DAY(NOW()),MAKEDATE(YEAR(NOW()),32),MAKETIME(10,21,23),PERIOD_ADD(20200202020202,10)
FROM DUAL;

# 日期的格式化与解析(重点!!!)
# 格式化：日期->字符串
# 解析：字符串->日期

#此时我们谈的是日期的显式格式化和解析

#之前，我们接触过隐式的格式化或解析
SELECT *
FROM employees
WHERE hire_date = '1987-6-17';

#解析:格式化的逆过程
SELECT GET_FORMAT(DATE,'USA')
FROM DUAL;

SELECT DATE_FORMAT(CURDATE(),GET_FORMAT(DATE,'USA'))
FROM DUAL;
```

## 5.4 流程控制函数

```mysql
#IF(VALUE,VALUE1,VALUE2)

SELECT last_name,commission_pct,IF(commission_pct IS NOT NULL,commission_pct,0)"details",
salary * 12 * (1+IF(commission_pct IS NOT NULL,commission_pct,0)) "annual_sal"
FROM employees;

#IFNULL(VALUE1,VALUE2):看做是IF(VALUE,VALUE1,VALUE2)的特殊情况

SELECT last_name,commission_pct,IFNULL(commission_pct,0)"details"
FROM employees;

#CASE WHEN ... THEN ... WHEN... THEN... ELSE ... END
#类似于java的if ... else if ... else if ... else
SELECT last_name,salary,CASE WHEN salary >= 15000 THEN 'Java后端开发'
			     WHEN salary >= 10000 THEN '前端开发'
			     WHEN salary >= 8000 THEN '测试开发'
			     ELSE '运维' END 'details',department_id
FROM employees;

#CASE ... WHEN ... THEN ... WHEN ... THEN ... ELSE... END
#类似于java中的switch ... case ... 
/*
练习1
查询部门号为10，20，30的员工信息，
若部门号为10，则打印其工资的1.1倍，
20号部门，则打印其工资的1.2倍，
30号部门，打印其工资的1.3倍，
其他部门，打印其工资的1.4倍
*/

SELECT employee_id,last_name,salary,department_id,CASE department_id WHEN 10 THEN salary * 1.1
								          							 WHEN 20 THEN salary * 1.2
			                                                         WHEN 30 THEN salary * 1.3
			                                                         ELSE salary * 1.4 END "details"
FROM employees;

/*
练习2
查询部门号为10，20，30的员工信息，
若部门号为10，则打印其工资的1.1倍，
20号部门，则打印其工资的1.2倍，
30号部门，打印其工资的1.3倍
*/

SELECT employee_id,last_name,salary,department_id,CASE department_id WHEN 10 THEN salary * 1.1
								          							 WHEN 20 THEN salary * 1.2
			                                                         WHEN 30 THEN salary * 1.3
			                                                         END "details"
FROM employees;
```


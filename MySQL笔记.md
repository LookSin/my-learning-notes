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


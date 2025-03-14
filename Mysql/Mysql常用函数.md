# MySQL 常用函数

## 1. 数值函数

### 1.1 ABS(x)
- **说明**：返回参数的绝对值。
- **范例**：
  ```sql
  SELECT ABS(-10); -- 结果为 10
  ```

### 1.2 CEILING(x)
- **说明**：返回大于或等于参数的最小整数。
- **范例**：
  ```sql
  SELECT CEILING(3.14); -- 结果为 4
  ```

### 1.3 FLOOR(x)
- **说明**：返回小于或等于参数的最大整数。
- **范例**：
  ```sql
  SELECT FLOOR(3.14); -- 结果为 3
  ```

### 1.4 RAND()
- **说明**：返回一个0到1之间的随机浮点值。
- **范例**：
  ```sql
  SELECT RAND(); -- 返回一个0到1之间的随机数
  ```

### 1.5 ROUND(x, d)
- **说明**：返回参数四舍五入后的值，`d`指定小数点后的位数。
- **范例**：
  ```sql
  SELECT ROUND(3.14159, 2); -- 结果为 3.14
  ```

## 2. 字符串函数

### 2.1 CONCAT(str1, str2, ...)
- **说明**：将多个字符串连接成一个字符串。
- **范例**：
  ```sql
  SELECT CONCAT('Hello', ' ', 'World'); -- 结果为 'Hello World'
  ```

### 2.2 LENGTH(str)
- **说明**：返回字符串的字节长度。
- **范例**：
  ```sql
  SELECT LENGTH('你好'); -- 结果为 6（UTF-8编码下）
  ```

### 2.3 CHAR_LENGTH(str)
- **说明**：返回字符串的字符长度。
- **范例**：
  ```sql
  SELECT CHAR_LENGTH('你好'); -- 结果为 2
  ```

### 2.4 UPPER(str)
- **说明**：将字符串转换为大写。
- **范例**：
  ```sql
  SELECT UPPER('hello'); -- 结果为 'HELLO'
  ```

### 2.5 LOWER(str)
- **说明**：将字符串转换为小写。
- **范例**：
  ```sql
  SELECT LOWER('HELLO'); -- 结果为 'hello'
  ```

### 2.6 SUBSTRING(str, pos, len)
- **说明**：从字符串中提取子字符串，从`pos`位置开始，提取`len`个字符。
- **范例**：
  ```sql
  SELECT SUBSTRING('Hello World', 7, 5); -- 结果为 'World'
  ```

### 2.7 REPLACE(str, from_str, to_str)
- **说明**：将字符串`str`中的`from_str`替换为`to_str`。
- **范例**：
  ```sql
  SELECT REPLACE('hello world', 'world', 'MySQL'); -- 结果为 'hello MySQL'
  ```

## 3. 日期和时间函数

### 3.1 NOW()
- **说明**：返回当前日期和时间。
- **范例**：
  ```sql
  SELECT NOW(); -- 返回当前日期和时间
  ```

### 3.2 CURDATE()
- **说明**：返回当前日期。
- **范例**：
  ```sql
  SELECT CURDATE(); -- 返回当前日期
  ```

### 3.3 CURTIME()
- **说明**：返回当前时间。
- **范例**：
  ```sql
  SELECT CURTIME(); -- 返回当前时间
  ```

### 3.4 DATE_ADD(date, INTERVAL expr type)
- **说明**：将一个时间间隔加到日期上。
- **范例**：
  ```sql
  SELECT DATE_ADD('2024-01-01', INTERVAL 1 YEAR); -- 结果为 '2025-01-01'
  ```

### 3.5 DATE_SUB(date, INTERVAL expr type)
- **说明**：从日期中减去一个时间间隔。
- **范例**：
  ```sql
  SELECT DATE_SUB('2024-01-01', INTERVAL 1 YEAR); -- 结果为 '2023-01-01'
  ```

### 3.6 YEAR(date)
- **说明**：返回日期的年份。
- **范例**：
  ```sql
  SELECT YEAR('2024-01-01'); -- 结果为 2024
  ```

### 3.7 MONTH(date)
- **说明**：返回日期的月份。
- **范例**：
  ```sql
  SELECT MONTH('2024-01-01'); -- 结果为 1
  ```

### 3.8 DAY(date)
- **说明**：返回日期的天数。
- **范例**：
  ```sql
  SELECT DAY('2024-01-01'); -- 结果为 1
  ```

## 4. 聚合函数

### 4.1 COUNT(column)
- **说明**：返回指定列的非空值的数量。
- **范例**：
  ```sql
  SELECT COUNT(*) FROM employees; -- 返回表中所有行的数量
  ```

### 4.2 SUM(column)
- **说明**：返回数值列的总和。
- **范例**：
  ```sql
  SELECT SUM(salary) FROM employees; -- 返回所有员工工资的总和
  ```

### 4.3 AVG(column)
- **说明**：返回数值列的平均值。
- **范例**：
  ```sql
  SELECT AVG(salary) FROM employees; -- 返回所有员工工资的平均值
  ```

### 4.4 MAX(column)
- **说明**：返回数值列的最大值。
- **范例**：
  ```sql
  SELECT MAX(salary) FROM employees; -- 返回所有员工工资的最大值
  ```

### 4.5 MIN(column)
- **说明**：返回数值列的最小值。
- **范例**：
  ```sql
  SELECT MIN(salary) FROM employees; -- 返回所有员工工资的最小值
  ```

## 5. 条件函数

### 5.1 IF(expr, true_val, false_val)
- **说明**：如果`expr`为真，则返回`true_val`，否则返回`false_val`。
- **范例**：
  ```sql
  SELECT IF(salary > 50000, 'High', 'Low') AS salary_level FROM employees;
  ```

### 5.2 CASE WHEN ... THEN ... ELSE ... END
- **说明**：多条件分支选择。
- **范例**：
  ```sql
  SELECT name, 
         CASE 
             WHEN salary > 50000 THEN 'High'
             WHEN salary BETWEEN 30000 AND 50000 THEN 'Medium'
             ELSE 'Low'
         END AS salary_level
  FROM employees;
  ```

## 6. 转换函数

### 6.1 CAST(expr AS type)
- **说明**：将表达式转换为指定的数据类型。
- **范例**：
  ```sql
  SELECT CAST('123' AS SIGNED); -- 将字符串转换为整数
  ```

### 6.2 CONVERT(type, expr)
- **说明**：将表达式转换为指定的数据类型。
- **范例**：
  ```sql
  SELECT CONVERT(DATE, '2024-01-01'); -- 将字符串转换为日期
  ```

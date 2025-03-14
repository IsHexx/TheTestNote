### MySQL 性能优化相关

#### 1. 相关概念

**1.1 Rowid 的概念**

- **说明**：MySQL中没有`ROWID`的概念，但可以通过`PRIMARY KEY`或`UNIQUE KEY`来唯一标识一行数据。

- **范例**：

  

  ```sql
  SELECT * FROM employees WHERE id = 1;
  ```

**1.2 Recursive SQL 概念**

- **说明**：MySQL在执行DDL语句时，也会执行一些额外的SQL语句来更新数据字典信息。这些额外的SQL语句称为`recursive SQL`。

- **范例**：

  ```sql
  CREATE TABLE new_table AS SELECT * FROM old_table;
  ```

**1.3 Row Source（行源）**

- **说明**：查询中由上一操作返回的符合条件的行的集合。

- **范例**：

  ```sql
  SELECT * FROM employees WHERE age > 30;
  ```

**1.4 Predicate（谓词）**

- **说明**：查询中的`WHERE`限制条件。

- **范例**：

  ```sql
  SELECT * FROM employees WHERE age > 30 AND department = 'Sales';
  ```

**1.5 Driving Table（驱动表）**

- **说明**：在嵌套循环连接中，先被访问的表称为驱动表。

- **范例**：

  ```sql
  SELECT e.name, d.department_name
  FROM employees e
  JOIN departments d ON e.department_id = d.department_id;
  ```

**1.6 Probed Table（被探查表）**

- **说明**：在嵌套循环连接中，被驱动表访问的表称为被探查表。

- **范例**：

  ```sql
  SELECT e.name, d.department_name
  FROM employees e
  JOIN departments d ON e.department_id = d.department_id;
  ```

**1.7 组合索引（concatenated index）**

- **说明**：由多个列构成的索引。

- **范例**：

  ```sql
  CREATE INDEX idx_emp ON employees (age, department_id);
  ```

**1.8 可选择性（selectivity）**

- **说明**：比较列中唯一键的数量和表中的行数，判断该列的可选择性。

- **范例**：

  ```sql
  SELECT COUNT(DISTINCT age) / COUNT(*) AS selectivity FROM employees;
  ```

**1.9 全表扫描（Full Table Scans，FTS）**

- **说明**：读取表中所有行，并检查每一行是否满足`WHERE`限制条件。

- **范例**：

  ```sql
  SELECT * FROM employees WHERE age > 30;
  ```

**1.10 通过ROWID的表存取（Table Access by ROWID或rowid lookup）**

- **说明**：通过`PRIMARY KEY`或`UNIQUE KEY`快速定位到目标数据。

- **范例**：

  ```sql
  SELECT * FROM employees WHERE id = 1;
  ```

**1.11 索引扫描（Index Scan或index lookup）**

- **说明**：通过索引查找数据。

- **范例**：

  ```sql
  SELECT * FROM employees WHERE age = 30;
  ```

#### 2. SQL 执行顺序及原理

**2.1 执行原理**

- **步骤**：
  1. **语法分析**：检查SQL语句是否符合语法。
  2. **语义分析**：检查表、字段等是否存在于数据库中。
  3. **权限检查**：检查用户是否有权限访问数据。
  4. **查询优化**：选择最佳的执行计划。
  5. **执行计划**：根据优化器选择的执行计划执行SQL语句。

**2.2 执行顺序**

- **SQL查询语法结构**：

  ```sql
  SELECT DISTINCT <..>
    FROM <..> JOIN <..> ON <..>
   WHERE <..>
   GROUP BY <..>
  HAVING <..>
   ORDER BY <..>
  ```

- **SQL处理顺序**：

  1. `FROM`
  2. `ON`
  3. `JOIN`
  4. `WHERE`
  5. `GROUP BY`
  6. `HAVING`
  7. `SELECT`
  8. `DISTINCT`
  9. `ORDER BY`

#### 3. 性能优化

**3.1 常用优化规则**

- **避免使用\***：在`SELECT`子句中明确列出需要的列。

  ```sql
  SELECT name, age FROM employees;
  ```

- **用EXISTS替代IN**：`EXISTS`通常比`IN`更高效。

  ```sql
  SELECT * FROM employees WHERE EXISTS (SELECT 1 FROM departments WHERE department_id = employees.department_id);
  ```

- **用EXISTS替换DISTINCT**：`EXISTS`通常比`DISTINCT`更高效。

  ```sql
  SELECT * FROM employees WHERE EXISTS (SELECT 1 FROM departments WHERE department_id = employees.department_id);
  ```

- **用>=替代>**：避免不必要的全表扫描。

  ```sql
  SELECT * FROM employees WHERE age >= 30;
  ```

- **用UNION ALL替代UNION**：`UNION ALL`不进行排序，效率更高。

  ```sql
  SELECT * FROM employees WHERE age > 30 UNION ALL SELECT * FROM employees WHERE age < 20;
  ```

- **使用索引**：确保查询列上有适当的索引。

  ```sql
  CREATE INDEX idx_age ON employees (age);
  ```

- **避免在索引列上使用函数**：函数会阻止索引的使用。

  ```sql
  SELECT * FROM employees WHERE age = 30; -- 使用索引
  SELECT * FROM employees WHERE YEAR(birthdate) = 1990; -- 不使用索引
  ```

- **选择最有效的表名顺序**：将小表放在前面，大表放在后面。

  ```sql
  SELECT * FROM small_table s JOIN large_table l ON s.id = l.id;
  ```

- **使用WHERE子句替换HAVING子句**：`HAVING`子句在过滤数据时效率较低。

  ```sql
  SELECT department_id, COUNT(*) FROM employees GROUP BY department_id HAVING COUNT(*) > 10;
  ```

- **避免在索引列上使用NOT**：`NOT`会阻止索引的使用。

  ```sql
  SELECT * FROM employees WHERE age != 30; -- 不使用索引
  SELECT * FROM employees WHERE age > 30; -- 使用索引
  ```

- **避免在索引列上使用OR**：`OR`会阻止索引的使用。

  ```sql
  SELECT * FROM employees WHERE age = 30 OR age = 40; -- 不使用索引
  SELECT * FROM employees WHERE age IN (30, 40); -- 使用索引
  ```

- **避免在索引列上使用LIKE的前缀通配符**：`LIKE`的前缀通配符会阻止索引的使用。

  ```sql
  SELECT * FROM employees WHERE name LIKE '%Alice'; -- 不使用索引
  SELECT * FROM employees WHERE name LIKE 'Alice%'; -- 使用索引
  ```

**3.2 索引**

- **索引唯一扫描（index unique scan）**

  - **说明**：通过唯一索引查找一个数值，通常返回单个`ROWID`。

  - **范例**：

    ```sql
    SELECT * FROM employees WHERE id = 1;
    ```

- **索引范围扫描（index range scan）**

  - **说明**：通过索引查找多个数值。

  - **范例**：

    ```sql
    SELECT * FROM employees WHERE age > 30;
    ```

- **索引全扫描（index full scan）**

  - **说明**：扫描索引中的所有数据块。

  - **范例**：

    ```sql
    SELECT * FROM employees ORDER BY age;
    ```

- **索引快速扫描（index fast full scan）**

  - **说明**：扫描索引中的所有数据块，但不进行排序。

  - **范例**：

    ```sql
    SELECT * FROM employees;
    ```

**3.3 执行计划**

- **查看执行计划**

  - **范例**：

    ```sql
    EXPLAIN SELECT * FROM employees WHERE age > 30;
    ```

- **执行计划常用列字段解释**

  - **Rows**：返回结果集的行数。
  - **Bytes**：返回结果集的字节数。
  - **Cost**：执行该步骤的估计成本，用于说明SQL执行的代价，理论上越小越好。
  - **Time**：Oracle估计的当前操作所需的时间。
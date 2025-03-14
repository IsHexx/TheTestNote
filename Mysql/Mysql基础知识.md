# Mysql基础知识

### 1. 数据操作（DML）

#### 1.1 数据查询

- **基本查询**

  - **语句**：`SELECT column FROM table WHERE condition;`

  - **范例**：

    ```sql
SELECT name, age FROM students WHERE age > 20;
    ```
    
  - **说明**：从`students`表中查询`name`和`age`列，条件是`age`大于20。

- **排序**

  - **语句**：`SELECT column FROM table ORDER BY column ASC|DESC;`

  - **范例**：

    ```sql
SELECT name, age FROM students ORDER BY age DESC;
    ```
    
  - **说明**：从`students`表中查询`name`和`age`列，并按`age`列降序排序。

- **分页**

  - **语句**：`SELECT column FROM table LIMIT offset, rows;`

  - **范例**：

    ```sql
SELECT name, age FROM students LIMIT 0, 5; -- 获取第一页，每页5条
    ```
    
  - **说明**：从`students`表中查询`name`和`age`列，获取第一页数据，每页显示5条记录。

- **分组与聚合**

  - **语句**：`SELECT column, COUNT(*), SUM(column) FROM table GROUP BY column;`

  - **范例**：

    ```sql
SELECT gender, COUNT(*), AVG(age) FROM students GROUP BY gender;
    ```
    
  - **说明**：从`students`表中按`gender`分组，统计每组的记录数和平均年龄。

- **连接查询**

  - **内连接**

    - **语句**：`SELECT a.column, b.column FROM table1 a INNER JOIN table2 b ON a.id = b.id;`

    - **范例**：

      ```sql
SELECT s.name, r.score FROM students s INNER JOIN results r ON s.id = r.student_id;
      ```
      
    - **说明**：从`students`表和`results`表中查询学生的名字和成绩，条件是两个表的`id`字段相等。

  - **左连接**

    - **语句**：`SELECT a.column, b.column FROM table1 a LEFT JOIN table2 b ON a.id = b.id;`

    - **范例**：

      ```sql
SELECT s.name, r.score FROM students s LEFT JOIN results r ON s.id = r.student_id;
      ```
    
    - **说明**：从`students`表和`results`表中查询学生的名字和成绩，即使`results`表中没有匹配的记录，也会显示`students`表中的记录，未匹配的字段显示为`NULL`。

#### 1.2 插入数据

- **插入单条数据**

  - **语句**：`INSERT INTO table (column1, column2) VALUES (value1, value2);`

  - **范例**：

    ```sql
INSERT INTO students (name, age) VALUES ('Alice', 22);
    ```
    
  - **说明**：向`students`表中插入一条记录，`name`为`Alice`，`age`为22。

- **插入多条数据**

  - **语句**：`INSERT INTO table (column1, column2) VALUES (value1, value2), (value3, value4);`

  - **范例**：

    ```sql
INSERT INTO students (name, age) VALUES ('Bob', 23), ('Charlie', 21);
    ```
    
  - **说明**：向`students`表中插入两条记录，分别是`Bob`（23岁）和`Charlie`（21岁）。

#### 1.3 更新数据

- **更新数据**

  - **语句**：`UPDATE table SET column1 = value1, column2 = value2 WHERE condition;`

  - **范例**：

    ```sql
UPDATE students SET age = 24 WHERE name = 'Alice';
    ```
    
  - **说明**：将`students`表中`name`为`Alice`的记录的`age`字段更新为24。

#### 1.4 删除数据

- **删除数据**

  - **语句**：`DELETE FROM table WHERE condition;`

  - **范例**：

    ```sql
DELETE FROM students WHERE name = 'Bob';
    ```
    
  - **说明**：从`students`表中删除`name`为`Bob`的记录。

- **清空表数据**

  - **语句**：`TRUNCATE TABLE table;`

  - **范例**：

    ```sql
TRUNCATE TABLE students;
    ```
    
  - **说明**：清空`students`表中的所有数据，但保留表结构。

#### 1.5 数据复制

- **复制表结构和数据**

  - **语句**：`CREATE TABLE new_table SELECT * FROM old_table;`

  - **范例**：

    ```sql
CREATE TABLE students_backup SELECT * FROM students;
    ```
    
  - **说明**：创建一个新表`students_backup`，并复制`students`表的所有数据和结构。

- **仅复制表结构**

  - **语句**：`CREATE TABLE new_table LIKE old_table;`

  - **范例**：

    ```sql
CREATE TABLE students_structure LIKE students;
    ```
    
  - **说明**：创建一个新表`students_structure`，仅复制`students`表的结构，不复制数据。

------

### 2. 表定义（DDL）

#### 2.1 创建表

- **语句**：`CREATE TABLE table_name (column1 datatype, column2 datatype, ...);`

- **范例**：

  ```sql
CREATE TABLE employees (
      id INT AUTO_INCREMENT PRIMARY KEY,
      name VARCHAR(50) NOT NULL,
      position VARCHAR(50),
      salary DECIMAL(10, 2)
  );
  ```
  
- **说明**：创建一个名为`employees`的表，包含`id`（主键，自动增长）、`name`（非空）、`position`和`salary`字段。

#### 2.2 删除表

- **语句**：`DROP TABLE IF EXISTS table_name;`

- **范例**：

  ```sql
DROP TABLE IF EXISTS employees;
  ```
  
- **说明**：如果`employees`表存在，则删除该表。

#### 2.3 重命名表

- **语句**：`ALTER TABLE old_table_name RENAME TO new_table_name;`

- **范例**：

  ```sql
ALTER TABLE employees RENAME TO staff;
  ```
  
- **说明**：将`employees`表重命名为`staff`。

#### 2.4 增加字段

- **语句**：`ALTER TABLE table_name ADD COLUMN column_name datatype;`

- **范例**：

  ```sql
ALTER TABLE staff ADD COLUMN email VARCHAR(100);
  ```
  
- **说明**：向`staff`表中添加一个新字段`email`，类型为`VARCHAR(100)`。

#### 2.5 修改字段

- **语句**：`ALTER TABLE table_name MODIFY COLUMN column_name datatype;`

- **范例**：

  ```sql
ALTER TABLE staff MODIFY COLUMN email VARCHAR(150);
  ```
  
- **说明**：将`staff`表中的`email`字段类型修改为`VARCHAR(150)`。

#### 2.6 重命名字段

- **语句**：`ALTER TABLE table_name CHANGE old_column_name new_column_name datatype;`

- **范例**：

  ```sql
ALTER TABLE staff CHANGE COLUMN email contact_email VARCHAR(150);
  ```
  
- **说明**：将`staff`表中的`email`字段重命名为`contact_email`，并保持类型为`VARCHAR(150)`。

#### 2.7 删除字段

- **语句**：`ALTER TABLE table_name DROP COLUMN column_name;`

- **范例**：

  ```sql
ALTER TABLE staff DROP COLUMN contact_email;
  ```
  
- **说明**：从`staff`表中删除`contact_email`字段。

#### 2.8 添加主键

- **语句**：`ALTER TABLE table_name ADD PRIMARY KEY (column_name);`

- **范例**：

  ```sql
ALTER TABLE staff ADD PRIMARY KEY (id);
  ```
  
- **说明**：将`staff`表的`id`字段设置为主键。

#### 2.9 删除主键

- **语句**：`ALTER TABLE table_name DROP PRIMARY KEY;`

- **范例**：

  ```sql
ALTER TABLE staff DROP PRIMARY KEY;
  ```
  
- **说明**：删除`staff`表的主键约束。

#### 2.10 创建索引

- **语句**：`CREATE INDEX index_name ON table_name (column_name);`

- **范例**：

  ```sql
CREATE INDEX idx_name ON staff (name);
  ```
  
- **说明**：在`staff`表的`name`字段上创建一个普通索引`idx_name`，用于加快按`name`字段查询的速度。

#### 2.11 删除索引

- **语句**：`DROP INDEX index_name ON table_name;`

- **范例**：

  ```sql
DROP INDEX idx_name ON staff;
  ```
  
- **说明**：删除`staff`表上的`idx_name`索引。

#### 2.12 索引相关

- **索引类型**

  - **主键索引**：`PRIMARY KEY`

    - **语句**：`ALTER TABLE table_name ADD PRIMARY KEY (column_name);`

    - **范例**：

      ```sql
ALTER TABLE staff ADD PRIMARY KEY (id);
      ```
      
    - **说明**：为主键列`id`添加主键索引，确保`id`列的值唯一且非空。

  - **唯一索引**：`UNIQUE`

    - **语句**：`CREATE UNIQUE INDEX index_name ON table_name (column_name);`

    - **范例**：

      ```sql
CREATE UNIQUE INDEX idx_email ON staff (email);
      ```
    
    - **说明**：创建一个唯一索引`idx_email`，用于`staff`表的`email`列，确保`email`列的值唯一。
  
  - **普通索引**：`INDEX`

    - **语句**：`CREATE INDEX index_name ON table_name (column_name);`

    - **范例**：

      ```sql
CREATE INDEX idx_position ON staff (position);
      ```
    
    - **说明**：创建一个普通索引`idx_position`，用于`staff`表的`position`列，可以加快按`position`列查询的速度。

  - **全文索引**：`FULLTEXT`
  
    - **语句**：`CREATE FULLTEXT INDEX index_name ON table_name (column_name);`

    - **范例**：

      ```sql
CREATE FULLTEXT INDEX idx_description ON articles (description);
      ```
    
    - **说明**：创建一个全文索引`idx_description`，用于`articles`表的`description`列，适用于全文搜索。

- **查看索引**

  - **语句**：`SHOW INDEX FROM table_name;`

  - **范例**：

    ```sql
SHOW INDEX FROM staff;
    ```
    
  - **说明**：查看`staff`表的所有索引信息。

- **使用索引**

  - **语句**：`EXPLAIN SELECT * FROM table_name WHERE condition;`

  - **范例**：

    ```sql
EXPLAIN SELECT * FROM staff WHERE name = 'Alice';
    ```
    
  - **说明**：通过`EXPLAIN`语句查看查询计划，确认是否使用了索引。

- **索引优化**

  - **避免在索引列上使用函数**：

    ```sql
-- 避免：SELECT * FROM staff WHERE YEAR(birthdate) = 1990;
    -- 推荐：SELECT * FROM staff WHERE birthdate BETWEEN '1990-01-01' AND '1990-12-31';
    ```
    
  - **避免在索引列上使用NOT操作符**：

    ```sql
-- 避免：SELECT * FROM staff WHERE NOT position = 'Manager';
    -- 推荐：SELECT * FROM staff WHERE position != 'Manager';
  ```
    
  - **避免在索引列上使用LIKE的前缀通配符**：
  
    ```sql
-- 避免：SELECT * FROM staff WHERE name LIKE '%Alice';
    -- 推荐：SELECT * FROM staff WHERE name LIKE 'Alice%';
  ```
  
- **索引的维护**

  - **优化表索引**：

    ```sql
OPTIMIZE TABLE staff;
    ```
    
    **说明**：优化`staff`表的索引，清理碎片，提高查询效率。

  - **重建索引**：

    ```sql
ALTER TABLE staff DROP INDEX idx_name;
    ALTER TABLE staff ADD INDEX idx_name (name);
  ```
    
    **说明**：删除并重新创建索引，可以解决索引碎片问题。

#### 2.13 创建视图

- **语句**：`CREATE VIEW view_name AS SELECT column FROM table WHERE condition;`

- **范例**：

  ```sql
CREATE VIEW senior_staff AS SELECT * FROM staff WHERE salary > 50000;
  ```
  
- **说明**：创建一个视图`senior_staff`，包含`staff`表中`salary`大于50000的记录。

#### 2.14 删除视图

- **语句**：`DROP VIEW IF EXISTS view_name;`

- **范例**：

  ```sql
DROP VIEW IF EXISTS senior_staff;
  ```
  
- **说明**：如果视图`senior_staff`存在，则删除该视图。

------

### 3. 存储引擎、用户和权限用户和权限（DCL）

#### 3.1 存储引擎

- **存储引擎**
  - **InnoDB**：默认存储引擎，支持事务、行级锁。
  - **MyISAM**：不支持事务，读操作性能高。

#### 3.2 用户授权

- **创建用户**

  - **语句**：`CREATE USER 'username'@'host' IDENTIFIED BY 'password';`

  - **范例**：

    ```sql
CREATE USER 'testuser'@'localhost' IDENTIFIED BY 'test123';
    ```
    
  - **说明**：创建一个用户`testuser`，只能从`localhost`登录，密码为`test123`。

- **授权**

  - **语句**：`GRANT privileges ON database_name.table_name TO 'username'@'host';`

  - **范例**：

    ```sql
GRANT SELECT, INSERT, UPDATE ON mydb.* TO 'testuser'@'localhost';
    ```
    
  - **说明**：授予用户`testuser`对数据库`mydb`中所有表的`SELECT`、`INSERT`和`UPDATE`权限。

- **撤销权限**

  - **语句**：`REVOKE privileges ON database_name.table_name FROM 'username'@'host';`

  - **范例**：

    ```sql
REVOKE UPDATE ON mydb.* FROM 'testuser'@'localhost';
    ```
    
  - **说明**：撤销用户`testuser`对数据库`mydb`中所有表的`UPDATE`权限。

- **删除用户**

  - **语句**：`DROP USER 'username'@'host';`

  - **范例**：

    ```sql
DROP USER 'testuser'@'localhost';
    ```
    
  - **说明**：删除用户`testuser`。

#### 3.3 临时表空间

- **创建临时表**

  - **语句**：`CREATE TEMPORARY TABLE temp_table (id INT, name VARCHAR(50));`

  - **范例**：

    ```sql
CREATE TEMPORARY TABLE temp_students (id INT, name VARCHAR(50));
    ```
    
  - **说明**：创建一个临时表`temp_students`，仅在当前会话中有效。

#### 3.4 分区表

- **分区表**：MySQL支持分区表，可以按`RANGE`、`LIST`等规则分区。

  - **按范围分区**：

    ```sql
CREATE TABLE range_partitioned (
        id INT,
        data VARCHAR(50)
    ) PARTITION BY RANGE (id) (
        PARTITION p0 VALUES LESS THAN (10),
        PARTITION p1 VALUES LESS THAN (20),
        PARTITION p2 VALUES LESS THAN MAXVALUE
    );
    ```
    
    **说明**：创建一个按`id`范围分区的表`range_partitioned`，分为三个分区`p0`、`p1`和`p2`。

  - **按列表分区**：

    ```sql
CREATE TABLE list_partitioned (
        id INT,
      data VARCHAR(50)
    ) PARTITION BY LIST (id) (
        PARTITION p0 VALUES IN (1, 2, 3),
        PARTITION p1 VALUES IN (4, 5, 6)
    );
    ```
    
    **说明**：创建一个按`id`列表分区的表`list_partitioned`，分为两个分区`p0`和`p1`。

#### 3.5 分区删除

- **删除分区**：

  ```sql
ALTER TABLE partitioned_table DROP PARTITION partition_name;
  ```
  
  **说明**：删除表`partitioned_table`中的分区`partition_name`。

#### 3.6 分区增加

- **增加分区**：

  ```sql
ALTER TABLE partitioned_table ADD PARTITION (
      PARTITION new_partition VALUES LESS THAN (30)
  );
  ```
  
  **说明**：向表`partitioned_table`中添加一个新分区`new_partition`，范围小于30。

#### 3.7 分区拆分

- **拆分分区**：

  ```sql
ALTER TABLE partitioned_table SPLIT PARTITION partition_name INTO (
      PARTITION new_partition1 VALUES LESS THAN (15),
      PARTITION new_partition2 VALUES LESS THAN (20)
  );
  ```
  
  **说明**：将表`partitioned_table`中的分区`partition_name`拆分为两个新分区`new_partition1`和`new_partition2`。

#### 3.8 分区合并

- **合并分区**：

  ```sql
ALTER TABLE partitioned_table MERGE PARTITION partition1, partition2 INTO PARTITION new_partition;
  ```
  
  **说明**：将表`partitioned_table`中的分区`partition1`和`partition2`合并为一个新分区`new_partition`。

#### 3.9 子分区

- **子分区**：MySQL支持子分区（二级分区）。

  - **创建子分区表**：

    ```sql
CREATE TABLE sub_partitioned (
        id INT,
        data VARCHAR(50)
    ) PARTITION BY RANGE (id)
    SUBPARTITION BY HASH (id) (
        PARTITION p0 VALUES LESS THAN (10) (
            SUBPARTITION sp0,
            SUBPARTITION sp1
        ),
        PARTITION p1 VALUES LESS THAN (20) (
            SUBPARTITION sp2,
            SUBPARTITION sp3
        )
    );
    ```
    
    **说明**：创建一个按`id`范围分区和哈希子分区的表`sub_partitioned`。

#### 3.10 范例分区表

- **示例**：创建一个按`RANGE`分区的表，并添加子分区。

  ```sql
CREATE TABLE example_partitioned (
      id INT,
      data VARCHAR(50)
  ) PARTITION BY RANGE (id)
  SUBPARTITION BY HASH (id) (
      PARTITION p0 VALUES LESS THAN (10) (
          SUBPARTITION sp0,
          SUBPARTITION sp1
      ),
      PARTITION p1 VALUES LESS THAN (20) (
          SUBPARTITION sp2,
          SUBPARTITION sp3
      )
  );
  ```
  
  **说明**：创建一个按`id`范围分区的表`example_partitioned`，并为每个分区添加两个子分区。

#### 3.11 数据字典

- **数据字典**：MySQL的数据字典存储了数据库的元数据，可以通过`INFORMATION_SCHEMA`数据库查询。

  - **查询表信息**：

    ```sql
SELECT * FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_SCHEMA = 'database_name';
    ```
    
    **说明**：查询数据库`database_name`中的所有表信息。

  - **查询索引信息**：

    ```sql
SHOW INDEX FROM table_name;
    ```
  
    **说明**：查看表`table_name`的所有索引信息。


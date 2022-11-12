# [Oracle]()

## 1.数据操作（DML）

### 1.1数据查询

```
select <列名> from <表名> [where <查询条件表达试>] [order by <排序的列名>[asc或desc]]
```

- 查询一列或者几列

```
查询一列:
select 列名 from 表名;
范例:
select name from test;		--查询test表里的name字段

查询多列:
select 列名1,列名2,列名3 from 表名;
范例:
select name, age, sex from test; --查询test表里的name,age,sex
```

- 条件查询

```
比较操作符: ">" "<" ">=" "<=" "<>" "!="  "between ..and" "like" "is null"
!= 不等于:
	select empno,ename,job from scott.emp where job!='manager';
^= 不等于: 
	select empno,ename,job from scott.emp where job^='manager'
<>不等于:  
    select empno,ename,job from scott.emp where job<>'manager';
<小于: 
	select sal from scott.emp where sal<1000;
>大于:
	select sal from scott.emp where sal>1000;
<=小于等于:
	select sal from scott.emp where sal<=1000;
>=大于等于:
	select sal from scott.emp where sal>=1000;
between...and 介于..与..间:
	select sal from scott.emp where sal  between 1000 and 2000;
not between...and 不介于..与..之间:
	select sal from scott.emp where sal  between 1000 and 2000;
like 模式匹配:
	select ename from scott.emp where ename like 'M%' (%表示任意长度的长度串);
	select ename from scott.emp where ename like 'M_' (_表示一个任意的字符)
is null 是否为空: 
	select ename from scott.emp where ename is null;
is not null 不为空:
	select ename from scott.emp where ename is not null;
in 在列表:
	select sal from scott.emp where sal in(1000,2000);
not in 不在列表:
	select sal from scott.emp where sal not in(1000,2000)

逻辑操作符:"or" "and" "not"
or(或):
select ename from scott.emp where ename='joke' or ename='jacky'
and(与):
select ename from scott.emp where ename='and' or ename='jacky'
not(非):
select ename from scott.emp where not ename='and' or ename='jacky'

集合操作符："union（并集）"  "union all（并集）"
union（并集）:
union连接两句sql语句， 两句sql语句的和 去掉重复的记录
(select deptno from scott.emp) union (select deptno from scott.dept);
union all（并集）:
连接两句sql语句，两句sql语句的和不用去掉重复的记录
(select deptno from scott.emp) union all (select deptno from scott.dept);

```

### 1.2插入数据

- insert into 表名 values(所有列的值);

```
insert into test values(1,'zhangsan');
```

- insert into 表名(列) values(对应的值);

```
insert into test(id,name) values(1,'zhangsan');
```

### 1.3更新数据

- update 表 set 列=新的值 [where 条件] -->更新满足条件的记录

```
update test set name='zhangsan2' where name='zhangsan'
```

- update 表 set 列=新的值 -->更新所有的数据

```
update test set age =20;
```

### 1.4删除数据

- delete from 表名 where 条件 -->删除满足条件的记录

```
delete from test where id = 1;
delete from test -->删除所有
commit; -->提交数据
rollback; -->回滚数据
delete方式可以恢复删除的数据，但是提交了，就没办法了 delete删除的时候，会记录日志 -->删除会比较慢
```

- truncate table 表名

```
删除所有数据，不会影响表结构，不会记录日志，数据不能恢复 -->删除快
```

- drop table 表名

```
删除所有数据，包括表结构一并删除，不会记录日志，数据不能恢复-->删除快
```

### 1.5数据复制

- 表数据复制

```
insert into table1 (select * from table2);
```

- 复制表结构

```
create table table1 select * from table2 where 1>1;
```

- 复制表结构和数据

```
create table table1 select * from table2;
```

- 复制指定字段

```
create table table1 as select id, name from table2 where 1>1;
```

## 2.**表定义

### 2.1创建表

- 创建新表

```
语法:
create table 表名
(
	列名1 数据类型(长度),
	列名2 数据类型(长度),
	...
	列名n 数据类型(长度)
);
comment on table 表名 is '注释内容';
comment on column 表名.列名  is '注释内容';
```

- 
  根据已有的表创建新表：

```
语法A：select * into table_new from table_old;   --(使用旧表创建新表)
语法B：create table tab_new as select col1,col2… from tab_old definition only;
```

### 2.2删除表

- drop table 表名

```
删除所有数据，包括表结构一并删除，不会记录日志，数据不能恢复-->删除快
```

### 2.3重命名表

- alter table 表名 rename to 新表名

```
--将表名testA修改为testB
alter table testA renam to testB;
```

### 2.4增加字段

- 加一列:alter table 表名 add 列名 数据类型(长度); 

```
范例:
alter table test_a add column_1 number(5);
```

- 加多列: alter table 表名 add(列名1 数据类型(长度), 列名2 数据类型(长度),列名3 数据类型(长度));-

```
范例:
alter table test_a add(e_name varchar2(20), e_date date, e_no number(10));
```

### 2.5修改字段

- alter table 表名 modify (字段名 字段类型 默认值 是否为空);

```
范例:
alter table test_a modify age number(10);

注意:
1.如果要修改一列的数据类型,则此列必须为空;
2.varchar2类型可以扩张,也可以缩短,但长度必须大于列中现有数据的最大长度;
3.number类型可以扩长,不能缩短;
```

### 2.6重命名字段

- alter table 表名 rename column 列名 to 新列名 （其中：column是关键字）

```
范例:
alter table test rename column old_name to new_name;
```

### 2.7删除字段

```
说明：alter table 表名 drop column 字段名;
**eg：**alter table tablename drop column ID;
```

### 2.8添加主键

```
alter table tabname add primary key(col);
```

### 2.9删除主键

```
alter table tabname drop primary key(col)
```

### 2.10创建索引

- create index 索引名 on 表名(列名);--在表的某一列上建立单列索引

```
范例:
create index IDX_EMP_ENAME on emp(ename);--在emp表的ename列新建名为 IDX_EMP_NAME的索引;
```

- create index 索引名 on 表名(列1,列2);--组合索引

```
范例:
create index IND_EMP_ENAME_JOB on emp(ename,job);--在emp表的ename和job列新建一个组合索引;
```

- 注意

```
1.索引名字以IDX或者IND开头,索引名尽量体现出所在的表和列;
2.索引的名字必须是数据库唯一的,不能重复,且长度不超30位;
3.索引列尽量选择唯一性高的（重复数据小的）,更新少的列;
4.一个表的索引尽量控制在5个以内;
5.一列只能有一个单列索引;（单列不能重复建索引，但可以在其他组合索引中使用)；
6.复制表时,索引是不复制的;
7.初始化大量数据时,可以先把索引删除,数据插入后再新建;
8.组合索引使用时,必须用到第一列,且条件顺序跟索引列顺序保持一致;
```

### 2.11删除索引

```
drop index idxname
**注：**索引是不可更改的，想更改必须删除重新建。
```

### 2.12索引相关

- 将索引置为unusable:alter index 索引名 unusable;

```
范例:alter index IDX_EMP_NAME unusable;
```

- 重建索引:alter index 索引名 rebuild online;

```
范例:	alter index IDX_EMP_NAME rebuild online;
```

- USER_INDEXES 索引词典 user _indexes

```
范例:select * from USER_INDEXES where tiaojian;
```

### 2.13创建视图

- crate or replace view 视图名 as select 语句;

```
范例1:
create view v_emp as select * from emp;
create or replace view v_emp_sum as select deptno, count(*) CNT from emp group by deptno order by deptno asc;

范例2:
create view 视图名[(别名,别名,别名..)] 
as 
select 列名 [别名],列名 [别名],.. from 表名
[read only]
read only:表示视图是只读

grant create any view to 用户名; --给用户赋创建视图的权限
grant select any view to 用户名;--给用户赋读视图的权限
删除视图
drop view 视图名;

注意:
1.视图名字以v开头,比如v_emp, v_dept;
2.不是所有的视图都能更新和插入,比如经过聚合函数和group by得来的视图;
3.因为视图本身就是一个select查询语句,所以视图本身不提高查询效率(加快查询);
4.同样因为视图本身是一个select语句,所以视图是不占用空间的;
5.视图的主要作用是为了屏蔽数据,比如emp表,如果别人想看员工信息,但工资不能暴漏,此时就可以建一个视图,只包含员工编号,姓名和部分编号列,其余的全部隐去,将视图开放给别人,以达到目的;
```

### 2.14删除视图

```
drop view viewname
```

### 2.15表空间
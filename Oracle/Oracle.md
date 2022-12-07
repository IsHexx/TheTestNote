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

## 2.表定义

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

- 一个完整的建表脚本应包含如下5部分:

```
1.建表部分:create table

2.注释部分,包含表注释和列注释;

3.索引和约束,包含主键,外键;

4.同义词

5.授权
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

- 数据块代表了数据库中最小粒度的逻辑数据存储层次。其次是·表  ，表空间， 数据文件（.dbf文件）

#### 表空间和权限

```
表空间:表空间相当于表的容器,需要先建表空间,才能建表;

分类:临时表空间和正式表空间.其中临时表空间专供临时表使用;
```

#### 临时表空间

```
语法：
    create temporary tablespace 临时表空间名字
    tempfile '数据文件路径及名字'
    size --临时表空间初始大小
    autoextend on/off--自动扩展打开或者关闭
    next 大小 maxsize 大小--一次扩展的空间大小 和 表空间最大
    extent management local;--存储本地管理 (固定写法)

范例：
	create temporary tablespace TMP
    tempfile 'D:\oracledata\orcl\TMP'
    size 100M
    autoextend on
    next 40M MAXSIZE 100M
    extent management local;
```

#### 正式表空间

```
语法：
    create tablespace 表空间名字
    logging/nologging--是否记录日志
    datafile '表空间文件路径及名字'
    size --初始大小
    autoextend on/off --自动扩展打开或者关闭
    next 大小 maxsize 大小--一次扩展的大小 和 表空间最大
    extent management local;--存储本地管理(固定写法)

范例：
    create tablespace DEF
    logging
    datafile 'D:\oracledata\orcl\TMP'
    size 100M
    autoextend on
    next 40M MAXSIZE 100M
    extent management local;
```

#### 新建用户

```
语法:
    create user 用户名 identified by 密码
    default tablespace 正式表空间名字
    temporary tablespace 临时表空间名字;

范例:
    create user TIGER identified by TIGER
    default tablespace DEF
    temporary tablespace TMP;
```

#### 用户授权

```
语法：
    grant 权限1, 权限2 to 用户名;
    revoke 权限名 from 用户名;
    revoke dba from wang;
  
范例:
	grant connect, resource to TIGER;  
```

### 2.16序列

- 定义:一串有序的数字,使用一次,增长一次;

```
语法：
    create sequence 序列名
    start with --起始值
    minvalue --最小值
    maxvalue --最大值
    increment by--每次增长多少,也叫 步长
    cache 缓存值
    cycle 循环(不加即为不循环);

范例:
    create sequence seq_test1
    start with 100000000
    minvalue 100000000
    maxvalue 999999999
    increment by 1
    cache 5
    cycle;
```

- 序列的属性

```
currval:取序列的当前值
nextval:取序列的值,序列的值自增
create sequence seq_1 start with 1 increment by 1;
刚创建的序列,使用currval会报错,至少使用过一次nextval后才可调用 
```

- 序列的调用

```
序列名.属性名
select seq_1.nextval from dual;
select seq_1.currval from dual;
```

- 序列相关注意事项


```
1.如果用序列作为表的主键时,从其库中导入一些数据到表中,这时要检查导入的数据的id值是否比当前序列的值大,
  这时需要将序列的start with值修改为比导入的数据id值大的一个数,否则程序运行中会报主键冲突
**修改序列开始值**
	alter sequence 序列名 start with 开始值;	
	
2. 序列名字以SEQ_开头;
3. cache的值必须小于(maxvalue-minvalue)/increment by
4. 尽量不要使用序列排序;
5. 序列的两个方法: 序列名.nextval  和 序列名.currval;
```

### 2.17用户授权

- 授权

```
数据库跨用户访问表时,需要授权才可以.
```

- 权限划分

```
表,视图,临时表: insert, delete, update, select
函数,存储过程,包等: execute
```

- 语法

```
grant 权限1,权限2,权限3 on 表名 to 用户名;

范例:
    grant select,update on dept to TIGER;
    revoke 权限名 on 数据库对象 from 用户名;
```

- 注意

  ```
  1. 授权需要建表用户执行;
  2. 授权本着权限最小原则;
  ```

### 2.18同义词

- 定义

```
用表名代替用户名.表名 的形式, 叫同义词;
```

- 语法

```
create or replace public synonym 表名 for 用户名.表名;

范例:
    create or replace public synonym dept for scott.dept;
```

### 2.19分区表

- 分区方式

```
range--范围分区,比如按照时间分区
list--枚举分区,比如按照身份证号码最后一位
hash--按照哈希值分区,通常使用较少,因为分区不均匀
```

#### range分区

- 范例

```
create table range_test
(
      id1 number(3),
       name1 varchar2(30),
       date1 date
)
partition by range(date1)--按照date1列数据范围分区
(
          partition P2001 values less than(to_date('20200201','YYYYMMDD')),--2020年2月1号之前的数据全部在P2001分区
          partition P2002 values less than(to_date('20200301','YYYYMMDD')),--2月1日及之后,3月1日之前的数据在P2002分区
         partition P2003 values less than(to_date('20200401','YYYYMMDD')),--3月1日及之后,4月1日之前的数据在P2003分区
          partition P2004 values less than(to_date('20200501','YYYYMMDD')),
          partition P2005 values less than(to_date('20200601','YYYYMMDD')),
        partition P_MAX values less than(maxvalue)--最大分区,即6月1日及之后的数据,全部落在P_MAX分区
);
```

- 注意事项

```
1.表内分区名字不能重复,但不同的表可以有相同的分区名;
2.range分区都要有最大分区,保证数据完整性;
3.values less than后的数据须跟分区键(分区列)类型保持一致;
4.merge时分区键不能更新;
```

- 查询某一分区的数据

```
select * from 表名 partition(分区名);
```

#### list分区

- 范例

```
create table list_test

(
       id1 number(3),
       name1 varchar2(1),
       date1 date
)

partition by list(id1)--按照id1列的值list分区

(
  partition P01 values(1),--id1=1时数据落入此分区
  partition P02 values(2),--id1=2时数据落入此分区
  partition P03 values(3),--id1=3时数据落入此分区
  partition P04 values(4),
  partition P05 values(5),
  partition P_default values(default)--除以上列举出的值,其余数据落入此分区.       
);
```

#### 分区删除

```
alter table 表名 drop partition(分区名);
alter table range_test drop partition(P_MAX);
```

#### 分区增加

```
alter table 表名 add partition 分区名 values less than(值)/分区名 values(值);
alter table range_test add partition P2006 values less than(to_date('20200701','YYYYMMDD'));
alter table list_test add partition P06 values(6);

注意：新增分区只能在最后,max分区或者default分区一定是在最后的 
```

#### 分区拆分

```
alter table 表名 split partition 分区名 at(values less than(值)/ values(值)) into (partition 分区名1, partition 分区名2);

alter table range_test split partition P2005_06 at(to_date('20200601','YYYYMMDD')) into (partition P2005,partition P2006);
```

#### 分区合并

```
alter table 表名 merge partitions 分区名1,分区名2 into partition 分区名3;
alter table range_test merge partitions P2005,P2006 into partition P2005_06;

注意：邻近的分区才能合并
```

#### 子分区范例

```
create table range_list

(
       id1 number(3),
       name1 varchar2(3),
       date1 date
)
partition by range(date1)
subpartition by list(id1)
(
          partition P2001 values less than(to_date('20200201','YYYYMMDD'))

          (
                    subpartition P2001_1 values(1),
                    subpartition P2001_2 values(2),
                    subpartition P2001_3 values(3),
                    subpartition P2001_4 values(4),
                    subpartition P2001_5 values(5)

          ),
          partition P2002 values less than(to_date('20200301','YYYYMMDD'))

          (
                    subpartition P2002_1 values(1),
                    subpartition P2002_2 values(2),
                    subpartition P2002_3 values(3),
                    subpartition P2002_4 values(4),
                    subpartition P2002_5 values(5)
          )
);
```

#### 分区表数据字典

```
DBA_TAB_PARTITIONS
```

#### 子分区数据字典

```
DBA_TAB_SUBPARTITIONS
```

#### 索引分类:

```
分区索引: create index 索引名 on 表名(列名) local;

全局索引: create index 索引名 on 表名(列名) global--或者省略不写;
```

#### 分区交换

```
alter table 表名 exchange partition 分区名 with table 中间表名; 
```

```
分区交换时需要,源表,中间表需要保持表结构完全一致.
如果是双层分区的表交换,则中间表需要保持跟range分区保持一致即如果range分区是外层分区, 因为range下还有分区,所以中间表也需要有分区,即中间表是一个list分区表;
如果range分区是第二层分区,则交换时就是子分区交换(exchange subpartition),range下无分区,中间表是一个普通表即可.
```

### 2.20Dblink

- 作用

```
将多个不同地点的服务器的oracle数据库逻辑上看成一个数据库,也就是说在一个数据库中可以操作另一个远程的数据库中的对象。
```

- 创建语法

```
create public database link <DBLink名称> 
	connect to <被连接库的用户名> 
	identified by <被连接库的密码> 
	using '<connect_string：Oracle客户端工具建立的指向被连接库服务名>';
```

- 参数说明

```
dblink: 你所创建的database link的名字,
user和password:要连接的数据库的用户名和密码
connect_string:可以是经过Net Manager配置的(tnsnames.ora)且经测试可以连接的服务名，不过也更直接用tnsnames里的字符串：(DESCRIPTION =
    (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = even.oracle.com)(PORT = 1521)) ) 	(CONNECT_DATA = (SERVICE_NAME =orcl) )

通过SHOW PARAMETER GLOBAL_NAMES，可以查看到其值是FALSE或者TRUE
```

- GLOBAL_NAMES=FALSE的情况，则DBLINK的名称可以自定义，相关的过程如下：

```
1.实现从本地数据库连接到远端数据库服务器：
    a) 创建数据库链接（前提是已分配相应权限
        SQL>grant CREATE DATABASE LINK to hr;
        SQL>CREATE DATABASE LINK LinkRemoteTestDB CONNECT TO hr IDENTIFIED BY hr USING 				‘test’;
     当然也可以直接写连接字符串
        SQL>create database link LinkRemoteTestDB2 connect to hr identified by hr
        using ‘TEST =
        (DESCRIPTION =
        (ADDRESS = (PROTOCOL = TCP)(HOST = even.oracle.com)(PORT = 1521))
        (CONNECT_DATA =
        (SERVER = DEDICATED)
        (SERVICE_NAME = test)
        )
        )’;
		则创建了一个以hr用户和TEST数据库的链接LinkRemoteTestDB.
	b)使用database link来查询远程HR schema下的testdblink 表信息
		SQL>select * from testdblink@LinkRemoteTestDB;
    
2.远程服务器要配置监听并且启动它
3.本地服务器要配置tnsnames
```

- 对于GLOBAL_NAMES = TRUE的情况，数据库链接（DATABASE LINK）的名字必须和数据库的名字相同：

```
在本地服务器上执行下面语句使GLOBAL_NAMES=TRUE：
SQL>ALTER SYSTEM SET GLOBAL_NAMES=TRUE;
再查询时，会有如下的错误：
SQL> select * from testdblink@LinkRemoteTestDB;
select * from testdblink@LinkRemoteTestDB
*
ERROR at line 1:
ORA-02085: database link LINKREMOTETESTDB.REGRESS.RDBMS.DEV.US.ORACLE.COM
connects to TEST.REGRESS.RDBMS.DEV.US.ORACLE.COM
```

```
登录远端数据库，通过执行
SQL>SELECT * FROM GLOBAL_NAME; 得到其数据库全名为TEST.REGRESS.RDBMS.DEV.US.ORACLE.COM
用原方式
SQL> CREATE DATABASE LINK LinkRemoteTestDB CONNECT TO hr IDENTIFIED BY hr USING ‘test’;创建过程不会出错，
但执行
“select * fromtestdblink@LinkRemoteTestDB;”的时候，就会出现ORA-02085: database link LINKREMOTETESTDB.REGRESS.RDBMS.DEV.US.ORACLE.COM connects to TEST.REGRESS.RDBMS.DEV.US.ORACLE.COM的错误了

所以需要采用下面的方式创建DBLINK:
SQL> create database link TEST.REGRESS.RDBMS.DEV.US.ORACLE.COM connect to HR identified by HR using ‘TEST’;
```

```
Database link created.再次执行

SQL> select * from testdblink@TEST.REGRESS.RDBMS.DEV.US.ORACLE.COM;
SQL> UPDATE testdblink@TEST.REGRESS.RDBMS.DEV.US.ORACLE.COM t set t.name=‘WatsonModified’ where id=1;
SQL> select * from testdblink@TEST.REGRESS.RDBMS.DEV.US.ORACLE.COM;
```

- 范例

```
create database link TO_210
  connect to TEST
  identified by  "2-L-v41E6YT-_F_s45-d"
  using '(DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = 172.19.201.210)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SERVICE_NAME = SGPMDB)
    ) 
  )'; 
```

- 查看数据库Dblink

```
SQL>select owner,object_name from dba_objects where object_type=‘DATABASE LINK’;
SQL>select * from dba_db_links;
```

- 删除Dblink

```
SQL> drop database link LinkRemoteTestDB;
```

## 3.事务控制（Tool Command Language）

### 3.1事务的特性

```
1.原子性:一个事务,要么全提交,要么全回滚,是一个整体,是操作的做小单位;
2.一致性:事务发生前,查到的数据是一致的;事务发生后,数据是一致的.(并非事务发生前后保持一致)
3.持久性:当事务发生后,如果没有别的事务操作数据,那数据不会改变;
4.隔离性:两个之间相互隔离,互不影响;
```

### 3.2事务分为隐式事务和显示事务

#### 隐式事务

- 定义：不需要人为去调用commit或者rollback,只要sql语句执行之后,它操作数据就会持久化到数据库

```
1.set autocommit on
2.create,drop,grant,revoke等语句执行完成之后会自动提交事务
3.执行完一个update,delete,insert语句都会自动提交事务
```

#### 显式事务

- 定义：人为的调用commit或者rollback,来控制什么时候进行数据的持久化

```
1.set autocommit off
2.create,drop,grant,revoke等语句执行完成之后会自动提交事务
3.调用commit提交事务(数据的持久化),调用rollback回滚事务(将数据库中的数据,恢复到本次事务未处理前的状态)
```

### 3.3事务隔离

#### 隔离级别

```
serializable（串行化）：避免脏读，幻读，不可重复读
pepeatable（可重复读）：避免脏读 不可重复读
read committed（读已提交）:避免脏读 【Oracle自带】
read uncommitted（读未提交）：任何都不能保证
```

#### 不考虑事务隔离会发生的问题

- 脏读

```
一个事务读取到了另一个事务未提交的数据操作结果
```

- 不可重复读

```
事务 T1 读取某一数据后，事务 T2 对其做了修改，当事务 T1 再次读该数据时得到与前一次不同的 值。这样就发生了在一个事务内两次读到的数据是不一样的，因此称为是不可重复读。
```

- 幻读（虚读）

```
事务在操作过程中进行两次查询，第二次查询的结果包含了第一次查询中未出现的数据或者缺少了第一次查 询中出现的数据（并不要求两次查询的 SQL 语句相同）。这是因为在两次查询过程中有另外一个事务插入数据造 成的
```

### 3.4锁

- 共享锁(S锁)

```
 lock table on share mode,所有人都可以读取数据库表中的数据,但是不能对数据进行修改
```

- 排它锁

```
有一个拿到数据库中的某一条数据后,其它人不能对这条数据进行处理
```

- 悲观锁

```
有多个事务处理同一数据时,认为每个事务处理时,都会发生冲突,当1个事务在处理这条数据时,不允许其它事务操作这条数据(事务的等待)
```

- 乐观锁

```
有多个事务处理同一数据时,它认为第个事务处理时,都不会发生冲突(所有事务都可以处理这个数据),
当每个事务提交时,对数据库中的数据进行检查,
如果果发现异常,提示用户去处理
```

- 死锁

```
死锁一般事发生在两个事务中,第1个事务在处理时,拿到一个数据的操作权,
另一个事务也拿到了一条数据的操作权,
这时第1个事务要操作第2个事务正在处理的这条数据,
第2个事务也需要操作第1个事务操作的这条数据
```

- 解决表被锁

```
select b.SID,b.SERIAL# from v$locked_object a, v$session b where a.SESSION_ID=b.SID;
select * from DBA_OBJECTS;
alter system kill session 'sid,SERIAL#';
select * from biao for update;
```

## 4.数据库控制语言（Control Language）

- 定义：设置或者更改数据库用户或角色的权限

```
范围：
    1.GRANT、DENY、REVOKE、COMMIT、ROLLBACK等语句
    2.在默认状态下，只有sysadmin、dbcreator、db_owner或db_securityadmin等角色的成员才有权利执行数	据控制语言
```

### 4.1GRANT：授予访问权限

- 定义:GRANT语句是授权语句，它可以把语句权限或者对象权限授予给其他用户和角色

- 授予角色权限

```
语法：
    grant 权限名 to 用户名;
示例：   
    grant resource to WANG;
```

- 授予实体权限

```
语法：    
    grant 权限名 on 数据库对象 to 用户名;
示例：
	grant select, insert on QQINFO to wang;
	grant select on QQINFO to WANG with grant option; --具有传递性的权限
```

- 授予系统权限

```
语法：    
    grant select/insert/delect/update/crater any table to 用户名;
示例：
	grant select/insert/delect/update/crater any table to SG_MIS;
```

### 4.2DENY：拒绝授予权限

- 定义：DENY语句用于拒绝给当前数据库内的用户或者角色授予权限，并防止用户或角色通过其组或角色成员继承权限。

### 4.3REVOKE：撤销访问权限

- 定义:REVOKE语句是与GRANT语句相反的语句，它能够将以前在当前数据库内的用户或者角色上授予或拒绝的权限删除，但是该语句并不影响用户或者角色从其他角色中作为成员继承过来的权限。
- - 撤销角色权限

```
语法：
	revoke 权限名 from 用户名;
示例：
	revoke dba from wang;
```

- 撤销实体权限

```
语法：
	revoke 权限名 on 数据库对象 from 用户名;
示例：
	revoke select, insert on QQINFO to wang;
```

- 撤销系统权限

```
语法：
	revoke 权限名 from 用户名;
示例：
	revoke select/insert/delect/update/crater from SG_MIS；
```

### 4.4COMMIT与ROLLBACK

- Commit提交事务处理：当执行DML操作时，数据库并不会立刻修改表中数据，需要commit，数据库中的数据立刻修改

- ROLLBACK事务处理回退：可使数据变化失效；修改前的数据状态被恢复；锁被释放。

- 使用COMMIT 和 ROLLBACK语句的优点:

```
1.确保数据完整性。
2.数据改变被提交之前预览。
3.将逻辑上相关的操作分组。
```

- 提交或回滚前的数据状态：

```
1.改变前的数据状态是可以恢复的
2.执行 DML 操作的用户可以通过 SELECT 语句查询之前的修正
3.其他用户不能看到当前用户所做的改变，直到当前用户结束事务。
4.DML语句所涉及到的行被锁定，其他用户不能操作
```

- 提交后的数据状态：

```
1.数据的改变已经被保存到数据库中。
2.改变前的数据已经丢失。
3.所有用户可以看到结果。
4.锁被释放,其他用户可以操作涉及到的数据。
5.所有保存点被释放。
```

### 4.5SAVEPOINT：设置保存点

- 保存点：使用 SAVEPOINT 语句在当前事务中创建保存点。

```
Update ...
SAVEPOINT UPDATE_DONE;
```

- 回滚：使用 ROLLBACK TO SAVEPOINT 语句回滚到创建的保存点。

```
INSERT...
ROLLBACK TO UPDATE_DONE;
```

### 4.6查询权限分配情况

| 数据字典视图        | 描述                       |
| ------------------- | -------------------------- |
| ROLE_SYS_PRIVS      | 角色拥有的系统权限         |
| ROLE_TAB_PRIVS      | 角色拥有的对象权限         |
| USER_ROLE_PRIVS     | 用户拥有的的角色           |
| USER_TAB_PRIVS_MADE | 用户分配的关于表对象权限   |
| USER_TAB_PRIVS_RECD | 用户拥有的关于表对象权限   |
| USER_COL_PRIVS_MADE | 用户分配的关于列的对象权限 |
| USER_COL_PRIVS_RECD | 用户拥有的关于列的对象权限 |
| USER_SYS_PRIVS      | 用户拥有的系统权限         |

## 5.执行PLSQL语句

### 5.1无名块

#### 5.1.1框架结构

- PLSQL基本框架

```
declare
--代码的声明部分,包括变量,常量,类型等
--变量,常量的赋值
begin
--执行代码部分
end;
```

- 变量(variable)

```
变量:变量的值是可变的,赋值后,变量的值随着改变,赋值即生效;
变量以v_开头,定义语法: 变量名 数据类型(长度);
```

- 常量(constant):

```
常量:代表的值不能更改,即不能重复赋值;
常量以c_开头,定义语法: 常量名 constant 数据类型(长度);
```

- 赋值:

```
PLSQL中的赋值符号是 :=,即将:=后的数据赋值前面的变量名或者常量名;

赋值可以在声明处,也可以在代码执行部分.
```

- 输出:

- dbms_output.put_line();

- 注释:

```
单行注释:代码前加--
多行注释:用/* */保住需要被注释的代码即可;
```

- 范例:

```
declare--基本框架,声明部分
v_empno number(4) :=1234;--声明变量v_empno,类型位number,长度4位,且赋值 1234
c_ename constant varchar2(20) :='SMITH';--声明常量c_ename, 数据类型位varchar2,长度20,赋值位SMITH
begin--基本框架
  v_empno :=4321;--给变量v_empno重新赋值为4321
  --c_ename :='ALLEN';
  dbms_output.put_line(v_empno);--输出v_empno所代表的值
  dbms_output.put_line('c_ename: '||c_ename);--输出v_ename代表的值,且添加提示信息:c_ename: 
end;--基本框架
```

- 注意:

```
1.每一行后都需要有分号,除了declare和begin;
2.输出时,用单引号包裹的部分作为字符串直接原样打印,如果要显示变量的值,需要用||连接;
```

- 类型:

```
%type:列类型 变量名 表名.列名%TYPE 变量 与表中的这一列类型一致,相同的数据类型,相同的长度;
%rowtype: 行类型, 变量名 表名%ROWTYPE; 变量类型与表类型一致,相同的数据类型，相同的长度;
```

```
范例:
declare
 v_empno emp.empno%TYPE;--变量v_empno沿用emp表中empno列的数据类型和长度,两者保持一致;
 v_emp emp%ROWTYPE;--变量v_emp沿用emp表的数据类型,即可以认为v_emp中也有8列,且数据类型与表保持一致;
begin

end;
```

- 将表中数据打印:需要将查出的数据into到变量中,再通过打印变量的方式将数据输出;

```
范例:

declare
 v_empno emp.empno%TYPE;--变量v_empno沿用emp表中empno列的数据类型和长度,两者保持一致;
 v_emp emp%ROWTYPE;--变量v_emp沿用emp表的数据类型,即可以认为v_emp中也有8列,且数据类型与表保持一致;
begin
select empno into v_empno from emp where rownum<2;--将表中第一行的empno值塞进v_empno;
select * into v_emp from emp where rownum<2;--将表中第一行数据塞进v_emp变量;
dbms_output.put_line(v_empno);--将变量v_empno打印出来
dbms_output.put_line('EMPNO:'||v_emp.EMPNO||', '||'ENAME:'||v_emp.ENAME||', '||'JOB:'||v_emp.JOB||', '||'MGR:'|| v_emp.MGR); --将变量v_emp中的部分列打印出来
end;

 
注意：沿用表数据类型 查找列一定要注明v_emp.EMPNO 不可v_emp;
```

- 自定义类型:

  ```
  语法:
  type 类型名 is record
  (
  	列名 数据类型(长度),
  	列名 数据类型(长度),
  	....
  	列名 数据类型(长度)
  );
  变量名 类型名;
  注意使用时,变量中的列名需要保持跟类型中的列名一致.
  ```

  ```
  范例:
  declare
  type t_t1 is record
  (
       id1 number(4),
       name1 varchar2(30)
  );
  v_v1 t_t1;
  v_empno emp.empno%TYPE;
  v_ename emp.ename%TYPE;
  begin
       select empno, ename into v_v1 from emp where rownum<2;
       dbms_output.put_line('empno:'||v_v1.id1||','||'ename:'|| v_v1.name1);
       select empno into v_empno  from emp where ename='ALLEN';
       select ename into v_ename  from emp where ename='ALLEN';
      dbms_output.put_line(v_empno||','||v_ename);
  end;
  ```

- 键盘输入:

  ```
  &:使用时,会弹出对话框要求数据值,其后可以加提示信息.
  
  使用时有两种方式:
  1.在声明变量时使用&提示信息;
  2.代码中直接使用&提示信息;
  3.使用时注意字符串单引号的问题,在赋值和输入值时得有一处带单引号,否则会报错;
  ```

  ```
  范例:
  
      declare
      v_empno emp.empno%TYPE :=&empno;
      v_ename emp.ename%TYPE;
      v_empno1 emp.empno%TYPE;
      v_ename1 emp.ename%TYPE:=&员工姓名1;
      v_empno2 emp.empno%TYPE;
      v_ename2 emp.ename%TYPE:='&员工姓名2'
  
      begin
        select ename into v_ename from emp where empno=v_empno;
        dbms_output.put_line(v_ename);  
        select empno into v_empno1 from emp where ename='&员工姓名';
        dbms_output.put_line(v_empno1);  
        select empno into v_empno2 from emp where ename=v_ename2;
        dbms_output.put_line(v_empno2);
      end;
  ```

- 匿名块执行DML:

  ```
  范例：
      declare
       v_empno emp.empno%TYPE;
       v_emp emp%ROWTYPE;
      begin
      select empno into v_empno from emp where rownum<2;
      select * into v_emp from emp where rownum<2; 
      dbms_output.put_line(v_empno);
      dbms_output.put_line('EMPNO:'||v_emp.EMPNO||', '||'ENAME:'||v_emp.ENAME||', '||'JOB:'||v_emp.JOB||', '||'MGR:'|| v_emp.MGR); --将变量v_emp中的部分列打印出来
      insert into scores values('200','4-123',89);
      commit;
      delete from scores where sno=200;
      commit;
      update scores set score=90 where sno='101' and cno='3-105';
      commit;
      end;
  ```

- 匿名块执行DDL:（动态SQL）

  ```
  1.匿名块执行DDL时,需要将语句放进一个变量,用 execute immediate 变量 的方式执行;
  2.execute immediate 方式也可以执行DML语句,但执行select时 into动作需要放在 execute immediate之后;
  3.如果执行的DML中包含单引号,则需要将语句中的单引号替换为双引号,或者使用q'[]'方式,将语句放在[]中间;
  ```

  ```
  范例：
      declare
      v_sql varchar2(300);
      v_sql1 varchar2(300);
      v_empno emp.empno%TYPE;
      begin
      /*v_sql:='create index IND_SNO on scores(sno)';
      execute immediate v_sql;*/
      v_sql:='select empno from emp where ename=''SMITH''';
      execute immediate v_sql into v_empno;
      dbms_output.put_line('v_empno:'||v_empno);
      v_sql1:=q'[select empno from emp where ename='ALLEN']';
      execute immediate v_sql1 into v_empno;
      dbms_output.put_line('v_empno:'||v_empno);
      end;
  ```

- 绑定变量方式: 效率最高 

  ```
  范例：
      declare
      v_sql varchar2(300);
      v_sal number(10,2);
      begin
        v_sql:=q'[select sal from emp where empno=:1 and job=:2]';--之前是列名=值的方式,此时值改为变量,执行时传入值,叫做 绑定变量方式
        execute immediate v_sql into v_sal using 7499,'SALESMAN';
        dbms_output.put_line(v_sal);
      end; 
  ```

  ```
  补充定义部分赋值变量时方式:
  
      declare
      v_sql varchar2(300);
      v_sal number(10,2);
      v_empno emp.empno%TYPE:=&empno;
      begin
        v_sql:=q'[select sal from emp where empno=]'||v_empno|| q'[ and job='&Job']';
        dbms_output.put_line(v_sql);
        execute immediate v_sql into v_sal;
        dbms_output.put_line('v_sal: '||v_sal);
      end;
  ```

- 练习题:

  ```
  1.查出emp表中ename是‘JONES’的人的JOB;
  
  declare
    v_job emp.job%type;
  begin
    select job into v_job from emp where ename = 'JONES';
    dbms_output.put_line(v_job);
  end;
  ```

  ```
  2.使用%rowtype的方式定义变量,查出dept表的第一行数据,要求打印出所有的列;
  declare
    v_dept dept%rowtype;
  begin
    select * into v_dept from dept where rownum = 1;
    dbms_output.put_line('deptno:' || v_dept.deptno || ', dname:' ||
                      v_dept.dname || ', loc:' || v_dept.loc);
  end;
  ```

  ```
  3.使用匿名块新建一张EMP_0212的表,包含empno, ename和job三列;
  declare
    v_sql varchar2(500);
  begin
    v_sql := q'[create table EMP_0212
  (
  	empno number(4),
  	ename varchar2(30),
  	job varchar2(30)
  )]';
    execute immediate v_sql;
  end;
  ```

  ```
  4.设计一个计算器,从键盘输入两个值,得出其和;
  
  declare
    v1 number(10, 4) := &数值1;
    v2 number(10, 4) := &数值2;
  begin
    dbms_output.put_line('两个数的和是:'||(v1 + v2));
  end;
  ```

  ```
  5.使用绑定变量方式,输出EMP表中JOB是PRESIDENT的人的名字;
  
  declare
    v_sql  varchar2(500);
    v_name emp.ename%type;
  begin
    v_sql:= q'[select ename from emp where job= :1]';
    execute immediate v_sql
      into v_name
      using 'PRESIDENT';
    dbms_output.put_line(v_name);
  end;
  ```

#### 5.1.2流程控制

- if-else

```
语法:

if 表达式  then SQL语句
elsif 表达式 then SQL语句
elsif 表达式 then SQL语句
else SQL语句
end if;
```

```
范例:

declare 
v1 varchar2(2) :='&性别';
begin
if v1='男' then
  dbms_output.put_line('M');
  elsif v1='男' then
    dbms_output.put_line('F');
    else
      dbms_output.put_line('Others');
end if;
end;
```

```
注意事项:
1.有if就得有end if,成对出现;
2.分支判断中elsif写法要注意;
3.每个分支后都要有 then(else后没有);
```

- case when

```
等值:
    case 列名/表达式
    when 值 then 语句;
    when 值 then 语句;
    end case;
```

```
范围:
    case
    when 表达式 then 语句;
    when 表达式 then 语句;
    end case;
```

```
范例:
declare
v1 varchar2(2) :='&性别';
begin
  case v1
    when '男' then dbms_output.put_line('M');
    when '女' then dbms_output.put_line('F');
   end case;
end;
```

```
declare
v1 varchar2(2):='&性别';
begin
  case
    when v1='男' then dbms_output.put_line('M');
    when v1='女' then dbms_output.put_line('F');
   end case;
end; 
```

```
注意事项:
    1. 有case,就得有end case,成对出现;
    2. 每一个when 后都有then;
```

- 练习:

```
1.手动输入一个empno,如果sal<=1000,则sal增加500,如果sal>1000且sal<=1500,则sal增加550,如果sal>1500且sal<3000,sal增加600,其余的sal变为原来的1.2倍;
declare
  v_sal   emp.sal%TYPE;
  v_sal1  emp.sal%TYPE;
  v_empno emp.empno%TYPE := &empno;
begin
  select sal into v_sal from emp_0213 where empno = v_empno;
  dbms_output.put_line('Old:' || v_sal);
  if v_sal <= 1000 then
    update emp_0213 set sal = sal + 500 where empno = v_empno;
  elsif v_sal > 1000 and v_sal <= 1500 then
    update emp_0213 set sal = sal + 550 where empno = v_empno;
  elsif v_sal > 1500 and v_sal < 3000 then
    update emp_0213 set sal = sal + 600 where empno = v_empno;
  else
    update emp_0213 set sal = sal * 1.2 where empno = v_empno;
  end if;
  commit;
  select sal into v_sal1 from emp_0213 where empno = v_empno;
  dbms_output.put_line('New:' || v_sal1);
end;
```

```
2.手动输入一个deptno,如果deptno是10,则sal增加500, deptno是20,则sal增加600,其余的sal增加700;

declare
  v_sal    emp.sal%TYPE;
  v_deptno emp.deptno%TYPE := &输入一个deptno;
begin
  if v_deptno = 10 then
    update emp_0213 set sal = sal + 500 where deptno = v_deptno;
  elsif v_deptno = 20 then
    update emp_0213 set sal = sal + 600 where deptno = v_deptno;
  else
    update emp_0213 set sal = sal + 700 where deptno = v_deptno;
  end if;
  commit;
end;
```

```
3.键盘输入成绩,90或以上为优秀,70(包含70)-90为良好,60(包含60)-70为及格,其余情况为不及格;
declare
  v_score number(5, 2) := &请输入一个成绩;
begin
  if v_score >= 90 then
    dbms_output.put_line('优秀');
  elsif v_score >= 70 and v_score < 90 then
    dbms_output.put_line('良好');
  elsif v_score >= 60 and v_score < 70 then
    dbms_output.put_line('及格');
  else
    dbms_output.put_line('不及格');
  end if;
end;
```

#### 5.1.3循环

- loop语法:

```
declare
--定义循环变量
begin
loop
控制循环退出
循环变量自加
执行动作
end loop;
end;
```

```
范例:
declare
i number(10):=1;
begin
  loop
    i:=i+1;
    dbms_output.put_line(i);
    exit when i=10;
  end loop;
end;
```

- 练习题:

```
1.使用loop循环计算1-10的加和;
declare
i number(2):=1;
s number(3):=0;
begin
  loop
    s:=s+i;
    --dbms_output.put_line(s);
    i:=i+1;
    exit when i=11;
  end loop;
  dbms_output.put_line('总和是:'||s);
end;
```

```
2.计算下面级数当末项小于0.001时的部分和。 1/(1*2)+1/(2*3)+1/(3*4)+…+1/(n*(n+1))+ ……

declare
  i number(5):=1;
  s number(10, 4) := 0;
begin
  loop
    s := s + 1 / (i * (i + 1));
    exit when 1 /(i * (i + 1)) < 0.001;
    i := i + 1;
  end loop;
  dbms_output.put_line(i);
  dbms_output.put_line(to_char(s,'fm0.0000'));
end;
```

```
3.计算s=1*2+2*3+…+N*(N+1),当N=50的值

declare
i number(3):=1;
s number(6):=0;
begin
  loop
    s:=s+i*(i+1);
    exit when i=5;
    i:=i+1;
  end loop;
  dbms_output.put_line(s);
end;
```

```
4.编程序求满足不等式 1+3^2+5^2+…+N^2>2000的最小N值

declare
  i number(3) := 1;
  s number(5) := 0;
begin
  loop
    s := s + power(i,2);
    exit when s > 2000;
    i := i + 2;
  end loop;
  dbms_output.put_line(i);
  dbms_output.put_line(s);
end;
```

- while循环可以理解为将退出判断放到最前面的loop循环.

```
语法:
    declare
    定义循环变量
    begin
    while 循环变量退出判断 loop
    执行代码;
    循环变量自加;
    end loop;
    end; 
```

```
范例:
    declare
    i number(2):=1;
    begin
      while i<=10 loop
        dbms_output.put_line(i);
        i:=i+1;
      end loop;
    end;
```

- for语法:

```
declare
begin
for 变量名  in 范围 loop
执行代码;
end loop;
end;
```

```
范例:
declare
begin
for i in 1..10 loop
  dbms_output.put_line(i);
  end loop;
  end;--------1 2 3 4 5 6 7 8 9 10;  
```

```
注意事项:
1.使用for循环时,不需要定义循环变量, for 后跟的就是循环变量;
2.in后的范围,就是循环的范围, 规定了循环的起始和截至,且循环变量每次加1;
```

```
declare
begin
for 变量名 in(结果集或者查询语句) loop
语句
end loop;
end;
```

```
范例:
  declare 
  begin
    for v in(select * from dept) loop
      dbms_output.put_line('Deptno:'||v.deptno||', Dname:'||v.dname||', Loc:'||v.loc);
    end loop;
  end;
```

```
注意:
1.变量不需要定义,直接使用即可;
2.in后是一个结果集,可以理解为将结果集赋给变量,再通过循环的方式将变量打印出来;
```

- 循环嵌套 ：外循环一次 内循环循环1次

- 练习题:

```
1.尝试用while循环和for循环练习之前的4个题目;
(1):使用loop循环计算1-10的加和
--while
declare
i number(2):=1;
s number(3):=0;
begin
  while i<=10 loop
    s:=s+i;
    i:=i+1;
    end loop;
    dbms_output.put_line(s);
end;
 
--for
declare
  s number(3) := 0;
begin
  for i in 1 .. 10 loop
    s := s + i;
  end loop;
  dbms_output.put_line(s);
end;
```

```
(2):计算下面级数当末项小于0.001时的部分和。 1/(1*2)+1/(2*3)+1/(3*4)+…+1/(n*(n+1))
--while
declare
  i number(5) := 1;
  s number(10, 5) := 0;
begin
  while 1 / (i * (i + 1)) > 0.001 loop
    s := s + 1 / (i * (i + 1));
    i := i + 1;
  end loop;
  s := s + 1 / (i * (i + 1));
  dbms_output.put_line(i);
  dbms_output.put_line(to_char(s,'fm0.0000'));
end;
```

```
(3):计算s=1*2+2*3+…+N*(N+1),当N=50的值;
--while
declare
i number(2):=1;
s number(10):=0;
begin
  while i<=50 loop
    s:=s+i*(i+1);
    i:=i+1;
  end loop;
  dbms_output.put_line(s);
end;
 
--for
declare
s number(10):=0;
begin
  for i in 1..50 loop
    s:=s+i*(i+1);
  end loop;
  dbms_output.put_line(s);
end;
```

```
(4):编程序求满足不等式 1+3^2+5^2+…+N^2>2000的最小N值;
--while
declare
  i number(3) := -1;
  s number(5) := 0;
begin
  while s <= 2000 loop
    i := i + 2;
    s := s + power(i, 2);
    /*  dbms_output.put_line('i:'||i);
    dbms_output.put_line('s:'||s);*/
  end loop;
  dbms_output.put_line(i);
  dbms_output.put_line(s);
end;
```

```
2.某公司要根据雇员的职位来加薪，公司决定按下列加薪结构处理：
   Designation      Raise
   ------------     --------
   clerk            500
   salseman         1000
   analyst          1500
   otherwise        2000
编写一个程序块，接受一个雇员名，从emp_0213表中实现上述加薪处理;
declare
begin
  for v in(select * from emp_0213) loop
    if v.job='CLERK' then
      update emp_0213 set sal=sal+500 where empno=v.empno;
      elsif v.job='SALESMAN' then
        update emp_0213 set sal=sal+1000 where empno=v.empno;
        elsif v.job='ANALYST' then
          update emp_0213 set sal=sal+1500 where empno=v.empno;
          else
            update emp_0213 set sal=sal+2000 where empno=v.empno;
            end if;
  end loop;
  commit;
  end;
```

```
3.计算S=1!+2!+…+10!(阶乘, 1!=1, 2!=1x2, 3!=1x2x3)
declare
i number(2):=1;
j number(2);
s number(10):=0;
t number(10);
begin
  loop
    j:=1;
    t:=1;
    loop
      t:=j*t;
      dbms_output.put_line('t:'||t);
      exit when j=i;
      j:=j+1;
 
    end loop;
    s:=s+t;
   dbms_output.put_line('s:'||s);
   dbms_output.put_line('----------------');
   exit when i=10;
   i:=i+1;
  end loop;
  dbms_output.put_line(t);
  dbms_output.put_line(s);
end;
 
 
declare
m number:=1;
s number:=0;
 begin
   for n in 1..10 loop
    m:=m*n;
      s:=m+s;
      end loop;
     dbms_output.put_line(m);
      dbms_output.put_line(s);
     end;
 
 
 
declare
i number(2):=0;
s number(30):=1;
c number (30):=0;
begin
  loop
    i:=i+1;
    s:=s*i;
    dbms_output.put_line(i);
    dbms_output.put_line(s);
    c:=c+s;
  exit when i=10;
  end loop;
  dbms_output.put_line('总和是:'||c);
  end;
 
declare
s number(38):=0;
s1 number(38):=1;
begin
  for i in 1..10 loop   
    for j in 1..i loop
      s1:=s1*j;
      end loop;
      
      s:=s+s1;
      s1:=1;    
  end loop;
  dbms_output.put_line(s);
end;
```

- 循环退出:

```
1.exit:遇到exit,即退出循环,执行循环体之后的代码;
declare
  i number(2) := 0;
begin
  loop
    i := i + 1;
    if i = 5 then
      exit;
    end if;
    dbms_output.put_line(i);
    exit when i = 10;
  end loop;
  dbms_output.put_line('循环体外循环');
end;
```

```
2.return:遇到return,直接结束代码.之后的代码不再执行;
declare
  i number(2) := 0;
begin
  loop
    i := i + 1;
    if i = 5 then
      return;
    end if;
    dbms_output.put_line(i);
    exit when i = 10;
  end loop;
  dbms_output.put_line('循环体外循环');
end;
```

```
3.continue:遇到continue,跳过本次循环,进入下一次循环.循环体外的代码也会执行.
declare
  i number(2) := 0;
begin
  loop
    i := i + 1;
    if i = 5 then
      continue;
    end if;
    dbms_output.put_line(i);
    exit when i = 10;
  end loop;
  dbms_output.put_line('循环体外循环');
end;
 
 
 
declare
begin
/*dbms_output.put('a');
dbms_output.new_line();
dbms_output.put('c');*/
dbms_output.new_line();
dbms_output.put_line('b');
dbms_output.new_line();
dbms_output.put_line('c');
end;
```

#### 5.1.4游标

- 游标(cursor):游标就是一个内存中的结果集,游标中有一个指针指向第一行数据,每fetch一次,指针往后挪动一行,直到后面不再有数据.实际,游标就是内存换效率.

  ```
  语法:
  declare
  cursor 游标名 is select语句;
  定义变量;
  begin
  open 游标名;
  fetch 游标名 into 变量;
  执行操作;
  close 游标名;
  end;
  ```

  

```
范例:
declare
cursor cur_dept is select * from dept;
v_dept dept%rowtype;

begin
  open cur_dept;
  fetch cur_dept into v_dept; 
  dbms_output.put_line('Deptno:'||v_dept.deptno||', DNAME:'||v_dept.dname||', LOC:'||v_dept.loc);
  close cur_dept;
end;
```

游标与loop循环联合使用:

```
declare
cursor 游标名  is select语句;
定义变量;
begin
open 游标名;
loop--循环在open之后,fetch之前,因为每次fetch一行数据,需要多次fetch才能全部拿到数据.但open只需一次就好;
fetch 游标 into 变量;
exit when 游标名%NOTFOUND;--控制退出 
须放在fetch之后,否则会多打印一行数据,因为每fetch一次,往后移一行,如果放在fetch之前,
表中最后一行数据打印后,指针还在最后一行,下一次fetch后才会移动.再次fetch将空fetch进变量,
此时空不覆盖变量.所以出现最后两条重复.
end loop;--结束循环在关闭游标之前;
close 游标;
end;
```

```
范例:
declare
cursor cur_dept is select * from dept;
v_dept dept%rowtype;

begin
  open cur_dept;
  loop
  fetch cur_dept into v_dept;
  exit when cur_dept%NOTFOUND;
  dbms_output.put_line('Deptno:'||v_dept.deptno||', DNAME:'||v_dept.dname||', LOC:'||v_dept.loc);
  end loop;
  close cur_dept;
end;

declare
cursor cur_test is select * from dept where deptno=50;
v_dept dept%ROWTYPE;
begin
--select * into v_dept from dept where deptno=30;
v_dept.deptno:=10;
v_dept.DNAME:='ABC';
v_dept.loc:='TEST';
  open cur_test;
  fetch cur_test into v_dept;
  dbms_output.put_line('DEPTNO:'||v_dept.deptno||', DNAME:'||v_dept.dname||', LOC:'||v_dept.loc);
  close cur_test;
end;
```

```
--while循环

open 游标名;
fetch 游标名 into 变量;
while 游标名%found loop
  执行语句;
  fetch 游标名 into 变量;
end loop;
close 游标名;
end;

范例：

declare
cursor cur_name is select ename,deptno from emp;
rname varchar2(20);
qq_no number;
begin
  open cur_name;
  fetch cur_name into rname,qq_no;
  while cur_name%found loop
    dbms_output.put_line(rname||':'||qq_no);
    fetch cur_name into rname,qq_no;
  end loop;
  close cur_name;
end;
```

```
--for第一种
for 变量 in 游标名 loop
执行动作 （变量名.列名）；
end loop;
end;

declare
  cursor cur_dept is
    select * from dept;
begin
  for v1 in cur_dept loop
    dbms_output.put_line('Deptno:' || v1.deptno || ', DNAME:' || v1.dname ||
                         ', LOC:' || v1.loc);
  end loop;
end;

--for第二种

for 变量 in (查询) loop
执行动作 （变量名.列名）；
end loop;
end;

begin
  for qq in (select realname,qqno from qqinfo) loop
    dbms_output.put_line(qq.realname||':'||qq.qqno);
  end loop;
end;
```

```
参数:
游标名%ISOPEN:检查游标是否打开;值返回true和false,若为true,则说明游标打开,false则说明游标未打开;
游标名%FOUND:检查游标内是否还有数据,返回true则说明有数据,false则说明无数据;
游标名%NOTFOUND:检查游标是否有数据;只返回true或者false,返回true就退出,返回false则继续往下执行;
游标名%ROWCOUNT:统计本次执行行数(不是全部,只是本次循环);
SQLCODE:报错代码,ORA-数字;
SQLERRM: ERRM:error message缩写,错误信息;
```

- 游标分类:

```
隐式游标和显式游标:
显式游标:有游标定义的,叫显式游标;
范例:declare
cursor cur_dept is select * from dept;
v_dept dept%rowtype;
```

```
begin
  open cur_dept;
  loop
  fetch cur_dept into v_dept;
  exit when cur_dept%NOTFOUND;
  dbms_output.put_line('Deptno:'||v_dept.deptno||', DNAME:'||v_dept.dname||', LOC:'||v_dept.loc);
  end loop;
  close cur_dept;
end;
```

- 隐式游标:在匿名块执行增删改时,都有自动加一个游标,这种就是隐式游标;隐式游标属性默认名sql;

```
范例:
declare
v_count number(3);
begin
--insert into EXCHANGE_TEST values('A','A','A','A');
select count(*) into v_count from dept;
if SQL%FOUND then
  dbms_output.put_line('隐式游标存在');
  end if;
  commit;
end;
```

- 静态游标和动态游标:
  静态游标:游标在定义时是固定的select语句,这种就是静态游标;

```
范例:
declare
cursor cur_dept is select * from dept;
v_dept dept%rowtype;

begin
  open cur_dept;
  loop
  fetch cur_dept into v_dept;
  exit when cur_dept%NOTFOUND;
  dbms_output.put_line('Deptno:'||v_dept.deptno||', DNAME:'||v_dept.dname||', LOC:'||v_dept.loc);
  end loop;
  close cur_dept;
end;
```

- 动态游标:游标的select语句是动态SQL,这种就是动态游标

```
语法:
declare
type 类型名 is ref cursor;
游标名 类型名;
-- 游标名  sys_refcursor;
v_sql varchar2(300);
begin
v_sql:='';
open 游标名 for v_sql;
fetch 游标名  into 变量;
执行操作;
close 游标;
end;
```

```
动态游标
--当一个需要遍历sql中列名或表名不确定时，用动态sql

declare
type tyc is ref cursor;
c1 tyc;
v_sql varchar2(200);
v_name varchar2(20);
begin
 for t in (select t.table_name from user_tables t where t.TABLE_NAME like 'EMP%') loop
  v_sql:='select ename from ' ||t.table_name;
  dbms_output.put_line(v_sql); 
  open c1 for v_sql;
  fetch c1 into v_name;
  while c1%found loop
   dbms_output.put_line(v_name);
   fetch c1 into v_name;
  end loop;
  close c1;
 end loop;
end;
```

```
范例:
declare
type t_cur is ref cursor;
cur_dept t_cur;
  --cur_dept sys_refcursor;
  v_dept   dept%rowtype;
  v_sql    varchar2(300);
begin
  v_sql := 'select * from dept';
  open cur_dept for v_sql;
  loop
    fetch cur_dept
      into v_dept;
    exit when cur_dept%NOTFOUND;
    dbms_output.put_line(v_dept.deptno);
  end loop;
  close cur_dept;
end;
```

- 绑定变量:

  ```
  动态游标中,将原来的筛选条件 列名=值 替换为 列名=参数,在执行时再将值传入,这种叫 绑定变量; 绑定变量的方式处理大量数据时效率较高;
  ```

```
语法:
declare
v_sql varchar2(300);
游标名 sys_refcursor;
begin
v_sql:='列名=:1';
open 游标名 for v_sql using 值;
fetch 游标 into 变量名;
数操作
close 游标;
end;
```

```
范例:
declare
type t_cur is ref cursor;
cur_dept t_cur;
  --cur_dept sys_refcursor;
  v_dept   dept%rowtype;
  v_sql    varchar2(300);
begin
  v_sql := 'select * from dept where deptno=:1';
  open cur_dept for v_sql using 10;
  loop
    fetch cur_dept
      into v_dept;
    exit when cur_dept%NOTFOUND;
    dbms_output.put_line(v_dept.loc);
  end loop;
  close cur_dept;
end;
```

- 练习:

```
1.定义游标:列出每个员工的姓名,部门名称并编程显示第5个到第10个记录;
declare
cursor cur_emp_dept is select ename, dname from
(select a.ename,b.dname,rownum rn from emp a, dept b where a.deptno=b.deptno) where rn>=5 and rn<=10;
v_ename emp.ename%TYPE;
v_dname dept.dname%type;
/*type t_emp_dept is record
(
     ename emp.ename%type,
     dname dept.dname%type
);
v_emp_dept t_emp_dept;*/

begin
  open cur_emp_dept;
  loop
    fetch cur_emp_dept into v_ename, v_dname;
    --fetch cur_emp_dept into v_emp_dept;
    exit when cur_emp_dept%NOTFOUND;
    dbms_output.put_line('ENAME:'||v_ename||', DNAME:'||v_dname);
    --dbms_output.put_line('ENAME:'||v_emp_dept.ename||', DNAME'||v_emp_dept.DNAME);
  end loop;
  close cur_emp_dept;

end;
```

```
declare
cursor cur_emp_dept is select a.ename,b.dname from emp a, dept b where a.deptno=b.deptno;
type t_emp_dept is record
(
     ename emp.ename%type,
     dname dept.dname%type
);
v_emp_dept t_emp_dept;
begin
  open cur_emp_dept;
  loop
    fetch cur_emp_dept into v_emp_dept;
    exit when cur_emp_dept%NOTFOUND;
  if cur_emp_dept%ROWCOUNT<5 then 
    continue;
    elsif cur_emp_dept%ROWCOUNT>10 then
      exit;
    else
      dbms_output.put_line('ENAME:'||v_emp_dept.ename||', DNAME'||v_emp_dept.DNAME);
      end if;
  end loop;
  close cur_emp_dept;
end;
```

```
2.定义游标:从雇员表中显示工资大于3000的记录,只要姓名,部门编号和工资.编程显示其中的奇数记录;
declare
cursor cur_emp is select ename,deptno,sal from emp where sal>=3000;
v_ename emp.ename%type;
v_deptno emp.deptno%type;
v_sal emp.sal%type;
begin
  open cur_emp;
  loop
  fetch cur_emp into v_ename,v_deptno,v_sal;
  exit when cur_emp%NOTFOUND;
  if mod(cur_emp%ROWCOUNT,2)=1 then
    dbms_output.put_line(cur_emp%ROWCOUNT||'行: ENAME:'||v_ename||', DEPTNO:'||v_deptno||', SAL'||v_sal);
    end if;
  end loop;
  close cur_emp;
end;
```

```
3.用游标显示所有部门编号与名称,以及其所拥有的员工人数;
declare
  cursor cur_EMP_DEPT is
    select a.deptno, a.dname, nvl(c.CNT, 0)
      from dept a,
           (select b.deptno, count(*) CNT from emp b group by b.deptno) c
     where a.deptno = c.deptno(+);
  type t_emp_dept is record(
    deptno emp.deptno%type,
    dname  dept.dname%type,
    cnt    number(5));
  v_emp_dept t_emp_dept;
begin
  open cur_EMP_DEPT;
  loop
    fetch cur_EMP_DEPT
      into v_emp_dept;
    exit when cur_EMP_DEPT%NOTFOUND;
    dbms_output.put_line('DEPTNO:' || v_emp_dept.deptno || ', DNAME:' ||
                         rpad(v_emp_dept.dname,10,' ') || ', CNT:' || v_emp_dept.CNT);
  end loop;
  close cur_EMP_DEPT;
end;
```

```
4.用游标属性%rowcount实现输出前十个员工的信息;
declare
  cursor cur_emp is
    select * from emp;
  v_emp emp%rowtype;
begin
  open cur_emp;
  loop
    fetch cur_emp
      into v_emp;
    exit when cur_emp%NOTFOUND;
    if cur_emp%ROWCOUNT <= 10 then
      dbms_output.put_line('EMPNO:' || v_emp.EMPNO);
    else
      exit;
    end if;
  end loop;
  close cur_emp;
end;
```

```
5.通过使用游标来显示dept表中的部门名称,及其相应的员工列表(提示:可以使用双重循环);
declare
  cursor cur_dept is
    select * from dept;
  v_dept dept%rowtype;
  v_cnt  number(5);
begin
  open cur_dept;
  loop
    fetch cur_dept
      into v_dept;
    exit when cur_dept%NOTFOUND;
    dbms_output.put_line(v_dept.DNAME);
    dbms_output.put_line('------------------------');
    select count(*) into v_cnt from emp where deptno = v_dept.deptno;
    if v_cnt > 0 then
      for v in (select ename from emp where deptno = v_dept.deptno) loop
        dbms_output.put_line(v.ename);
      end loop;
    else
      dbms_output.put_line('null');
    end if;
    dbms_output.new_line();
  end loop;
  close cur_dept;
end;
```

```
6.接受一个部门号,使用For循环,从emp表中显示该部门的所有雇员的姓名,工作和薪水;
declare
  v_dept number(4) := &输入一个部门编号;
begin
  for v in (select ename, job, sal from emp where deptno = v_dept) loop
    dbms_output.put_line('ENAME:' || v.ename || ', JOB:' || v.job ||
                         ', SAL:' || v.sal);
  end loop;
end;
```

```
7.编写一个程序块,将emp表中前5人的名字,及其出的工资等级(salgrade)显示出来;
declare
  cursor cur_emp is
    select a.ename, b.grade
      from emp a, SALGRADE b
     where a.sal > b.losal
       and a.sal < b.hisal;
  v_ename emp.ename%type;
  v_grade salgrade.grade%type;
begin
  open cur_emp;
  loop
    fetch cur_emp
      into v_ename, v_grade;
    exit when cur_emp%NOTFOUND;
    if cur_emp%ROWCOUNT <= 5 then
      dbms_output.put_line('ENAME:' || v_ename || ', GRADE:' || v_grade);
    else
      exit;
    end if;
  end loop;
  close cur_emp;
end;
```

```
另解题思路：

5.declare 
n varchar2(2000) ;
cursor cur_dept is select * from dept ;
m dept%rowtype;
begin 
  open cur_dept ;
  loop
    fetch cur_dept into m ;
exit when cur_dept%NOTFOUND;
dbms_output.put_line(m.deptno||m.dname);

 for i in (select ename  from emp where emp.deptno=m.deptno) loop
 dbms_output.put_line(i.ename);
end loop;
end loop;
close cur_dept;
end;

declare
begin
  for d in (select * from dept) loop
    dbms_output.put_line(d.dname||':');
    for e in (select * from emp where deptno=d.deptno) loop
      dbms_output.put_line('   '||e.ename);
    end loop;
  end loop;
end;

3.declare
sl number;
begin
  for d in (select * from dept) loop
    select count(1) into sl from emp e where e.deptno=d.deptno;
    dbms_output.put_line(d.deptno||','||d.dname||','||sl);
  end loop;
end;

declare 
n number ;
cursor cur_dept is select * from dept ;
m dept%rowtype;
begin 
  open cur_dept ;
  loop
    fetch cur_dept into m ;
    select count(*) into n from emp where emp.deptno=m.deptno;
    exit when cur_dept%NOTFOUND;
    dbms_output.put_line(m.deptno||m.dname||n);
    end loop;
    close cur_dept;
    end;

2.declare
cursor cur_1 is select rn ,ename,deptno,sal from (select rownum rn,ename,deptno,sal from emp
where sal>1000);
type t1 is record
(rn number(5),
ename emp.ename%type,
deptno emp.deptno%type,
sal emp.sal%type
 );
v_1 t1;
begin
  open cur_1;
  loop
   fetch cur_1 into v_1;
   exit when cur_1%NOTFOUND;
   if mod (cur_1%rowcount,2)<>0 then
 dbms_output.put_line('部门是：'||v_1.deptno||' 工资是：'||v_1.sal||' 姓名是：'||v_1.ename||' 奇数是：'||v_1.rn);
   end if;
   end loop;
   close cur_1;
   end;

declare
cursor cur_1 is select ename,deptno,sal from emp where sal>1000;
type t1 is record
(
ename emp.ename%type,
deptno emp.deptno%type,
sal emp.sal%type
 );
v_1 t1;
n number :=0 ;
begin
  open cur_1;
  loop
    n:=n+1;
   fetch cur_1 into v_1;
   exit when cur_1%NOTFOUND;
 --dbms_output.put_line('部门是：'||v_1.deptno||' 工资是：'||v_1.sal||' 姓名是：'||v_1.ename||' 奇数是：'||n);
 if mod(n,2)<>0 then 
   dbms_output.put_line('部门是：'||v_1.deptno||' 工资是：'||v_1.sal||' 姓名是：'||v_1.ename||' 奇数是：'||n);
   end if;
   end loop;
   close cur_1;
   end;

declare
cursor cur_1 is select rn ,ename,deptno,sal from (select rownum rn,ename,deptno,sal from emp
where sal>1000);
type t1 is record
(rn number(5),
ename emp.ename%type,
deptno emp.deptno%type,
sal emp.sal%type
 );
v_1 t1;
begin
  open cur_1;
  loop
   fetch cur_1 into v_1;
   exit when cur_1%NOTFOUND;
   if mod (v_1.rn,2)<>0 then
   dbms_output.put_line(v_1.deptno||v_1.sal||v_1.ename||v_1.rn);
   end if;
   end loop;
   close cur_1;
   end;

6.declare 
cursor cur_emp is select ename,job,sal from emp where deptno=&部门；;
begin
for i in cur_emp loop
dbms_output.put_line('姓名是: '||i.ename||'  工作是: '||i.job||'  工资是: '||i.sal);
  end loop;
  end;

declare 
begin
for i in (select ename,job,sal from emp where deptno=&部门) loop
dbms_output.put_line('姓名是: '||i.ename||'  工作是: '||i.job||'  工资是: '||i.sal);
  end loop;
  end;



7.declare
v emp%rowtype;
cursor cur_emp is select * from emp where rownum<=5; 
begin 
open cur_emp;
loop
  fetch cur_emp into v;
  exit when cur_emp%NOTFOUND;

 for i in (select * from salgrade) loop

​```
if v.sal between i.losal and i.hisal
​```

   then 
     dbms_output.put_line('姓名:  '||v.ename||'   等级:  '||i.grade);
    end if;
 end loop;
     end loop;
     close cur_emp;
     end;



declare
v emp%rowtype;
cursor cur_emp is select * from emp where rownum<=5; 
begin 
open cur_emp;
loop
  fetch cur_emp into v;
  exit when cur_emp%NOTFOUND;
  dbms_output.put_line(v.ename);

 for i in (select * from salgrade --where v.sal>losal and v.sal <hisal) loop
   dbms_output.put_line(i.grade);
 end loop;
 /* if v.sal between i.losal and i.hisal
   then 
     dbms_output.put_line(v.ename);
    end if;*/
     end loop;
     close cur_emp;
     end;
```

#### 5.1.5集合

- 定义：

```
他是存放数据类型相同的一组数据；
集合存赋值和取值都是根据下标来完成的；
plsql 中的存放的元素个数是不限制的；
```

- 
  赋值：变量名（下标):=值；

```
范例 
col(1):='a';
取值
变量名（下标）
dbms_output.put_line(col(1));
```

- 集合属性

```
变量名.first :  返回第一个下标的值；
变量名.last：返回最后下标的值；
变量名.next (下标):返回当前下标的下一个值；
变量名.prior (下标)：返回当前下标的先前的值；
变量名.count :返回集合元素个数或者数据条数，不包含空值；
变量名.limt:返回元素个数（针对变长组数）；
```

- 集合分类：

  ```
  索引表
  嵌套表
  变长数组；
  ```

- 索引表： 

```
索引表集合的一种，存放数据类型相同的一组数据，下标可以是数字或字符串；
使用前不需要初始化，也不需要扩展。
```

```
数字数据类型 :
pls_integer    下标范围 -2 31次方 到2 的·31次方之间 
binary_integer 超过pls_integer范围用 binary_integer
相比较pls_integer 效率高于binary_integer 因为pls_integer 会调用硬件计算
```

```
语法
type 名 is table of 存放数据数据类型 index by 下标数据类型；
变量名 类型名；

范例 
declare
type v is table of varchar2(30) index by pls_integer ; 
col  v;
begin 
col (1):='a';
col(5):='b';
col(3):='c';

dbms_output.put_line(col.first);
dbms_output.put_line(col.last);
dbms_output.put_line(col.next(3));
dbms_output.put_line (col.prior(3));
dbms_output.put_line(col.count);

end;
```

```
注意
    下标系统会默认排序； 并不是按着代码执行；

    记录型变量和集合变量的区别；
    记录型变量只存放一行数据；
    集合变量可以存放无限数据；

    循环与索引表使用 ：循环变量与下标类型保持一致；循环变量赋值在begin；
```

-  正序打印下标；

```
declare
type v is table of varchar2(30) index by pls_integer;
col v;
i number;
begin 
col(1):='a';
col(2):='b';
col(3):='c';
i:=col.first;
loop
dbms_output.put_line(i||':'||col(i));
---i:=col.next(i);
exit when i=col.last;
i:=col.next(i);
end loop;
end;

declare
type v is table of varchar2(30) index by pls_integer;
col v;
begin 
col(1):='a';
col(2):='b';
col(3):='c';
for i in  col.first ..col.last loop
dbms_output.put_line(i||':'||col(i));
end loop;
end;
```

-  倒叙打印下标

```
declare
type v is table of varchar2(30) index by pls_integer;
col v;
i number;
begin 
col(1):='a';
col(2):='b';
col(3):='c';
i:=col.last;
loop
dbms_output.put_line(i||':'||col(i));
---i:=col.next(i);
exit when i=col.first;
i:=col.prior(i);
end loop;
end;

declare
type v is table of varchar2(30) index by pls_integer;
col v;
i number;
begin 
col(1):='a';
col(2):='b';
col(3):='c';
i:=col.last;
while i>=col.first loop
dbms_output.put_line(i||':'||col(i));
i:=col.prior(i);
end loop;
end;

declare
type v is table of varchar2(30) index by pls_integer;
col v;
begin 
col(1):='a';
col(2):='b';
col(3):='c';
for i in reverse col.first ..col.last loop
dbms_output.put_line(i||':'||col(i));
end loop;
end;
```

- 嵌套表

```
是集合的一种，存放数据类型相同的一组数据，使用时需要初始化，扩展，下标只有数字
初始化可以初始空嵌套表 或者 初始有元素的嵌套表
```

```
语法
type 类型名 is table of 值的类型；
变量名 类型名；

赋值 变量名（下标)：=值；
取值 变量名（下标）；
```

```
范例（初始化空嵌套表）
declare 
type v is table of varchar2(30);--type 类型名 is table of 值的数据类型；
col v;----变量名 类型名；
begin 
col := v() ;----初始化一个空的嵌套表 变量名 赋值 类型名();
col.extend (3); ---空嵌套表扩展元素 变量名.extend(参数)；参数为扩展几个元素   
col(1):='a';-----赋值
col(2):='b';
col(3):='c';
dbms_output.put_line(col(1));---执行动作
end;
```

```
范例（初始化有元素嵌套表）
declare 
type v is table of varchar2(30);--type 类型名 is table of 值的数据类型；
col v;----变量名 类型名；
begin 
col := v('a','b','c'); ----初始化一个有元素嵌套表 变量名 赋值 类型名(元素);
col.extend (3); ---嵌套表扩展元素 变量名.extend(参数)；参数为扩展几个元素   
col(4):='aa';-----赋值
col(5):='bb';
col(6):='cc'; 

/*col := v('a','b','c'); ----初始化一个有元素嵌套表 变量名 赋值 类型名(元素);
col.extend(1,3)；--嵌套表扩展元素 并赋值；变量名.extend (参数1，参数2)
                           --参数一表示扩展元素个数，参数二表示扩展下标赋予参数2下标的值；*/

dbms_output.put_line(col(4));
dbms_output.put_line(col(5));
dbms_output.put_line(col(6));---执行动作
end;
```

- 变长数组

```
变长数组:是集合的一种,存放类型相同的一组数据,使用之前先初始化,需要扩展. 下标只有数字
```

```
语法:
type 类型名 is varray(数字) of 值数据类型;---varray(数字)代表变长数组元素上限；
变量名 类型名;
```

```
范例：
type v is varray(5) of varchar2(30);--变长组数元素上限为5 即 变量.limit为5；
col v;----变量名 类型名；
```

```
注意事项:
(1):变长数组使用前需要初始化,语法同嵌套表;
(2):增加新元素需要先扩展,语法同嵌套表;
```

```
范例（初始化空变长组表）
declare 
type v is varray(10) of varchar2(30);--type 类型名 is varray(10) of 值的数据类型；
col v;----变量名 类型名；
begin 
col := v() ;----初始化一个空的变长组表 变量名 赋值 类型名();
col.extend (3); ---空变长组表扩展元素 变量名.extend(参数)；参数为扩展几个元素   
col(1):='a';-----赋值
col(2):='b';
col(3):='c';
dbms_output.put_line(col(1));---执行动作
end;
```

```
范例（初始化有元素变长组表）
declare 
type v is varray(10) of varchar2(30);--type 类型名 is varray(10) of 值的数据类型；
col v;----变量名 类型名；
begin 
col := v('a','b','c'); ----初始化一个有元素变长组表 变量名 赋值 类型名(元素);
col.extend (3); --变长组表扩展元素 变量名.extend(参数)；参数为扩展几个元素   
col(4):='aa';-----赋值
col(5):='bb';
col(6):='cc'; 

/*col := v('a','b','c'); ----初始化一个有元素变长组表 变量名 赋值 类型名(元素);
col.extend(3,3)；--变长组表扩展元素 并赋值；变量名.extend (参数1，参数2)
                           --参数一表示扩展元素个数，参数二表示扩展下标赋予参数2下标的值；*/

dbms_output.put_line(col(4));
dbms_output.put_line(col(5));
dbms_output.put_line(col(6));---执行动作
dbms_output.put_line(col.limit)；--输出结果为10 即元素最大个数；
dbms_output.put_line(col.count)；--输出结果为6 空值不计数；
end;

【注】 如果扩展元素超过limit 则报错；
```

- 三种集合的区别:

```
相同点 都是集合的一种，存放类型相同的一组数据, 集合存赋值和取值都是根据下标来完成的；
不同点：
(1).索引表可以是数字,也可以是字符串;但嵌套表和变长数字下标只有数字;
(2).索引表使用前不需要初始化,也不需要扩展;嵌套表和变长数组需要初始化和扩展;
(3).索引表和嵌套表数据量无限制,但变长数组要小于limit的限制;
```

- 4.集合实际应用:

```
(1): bulk collect 嵌套表和变长数组和bulk collect联用时,不需要初始化和扩展.
declare
  type t_type is table of emp%ROWTYPE;--定义一个嵌套表,区别于之前定义的嵌套表只有一列,此处是一个表的类型,有8列;
  v_emp t_type;
begin
  select * bulk collect into v_emp from emp;--因为v_emp是一个嵌套表,如果不使用bulk collect则需要初始化和扩展
  for i in v_emp.first .. v_emp.last loop--因为v_emp是嵌套表,下标全是数字,且每次自增,和for循环的循环变量i相似, 之前讲for循环是 for i in 1..10,此处v_emp.first就是1,循环的开始,v_emp.last就是循环结束.
    dbms_output.put_line(v_emp(i).empno);
  end loop;
end;
```

```
(2):集合和游标结合:
declare
  cursor cur_emp is
    select * from emp;
  type t_type is table of emp%ROWTYPE;
  v_emp t_type;
begin
  open cur_emp;
  loop
    fetch cur_emp bulk collect into v_emp limit 10;--每次从游标中取10行数据放入集合,所以针对这个例子,需要fetch 3次
    exit when v_emp.count = 0;--控制退出 在这里放弃游标名%NOTFOUND的写法,因为数据量被limit值整除时,会报错 如果最后一次fetch的数据量不够limit的值,程序不会处理;
    for i in v_emp.first .. v_emp.last loop--因为集合中有10行数据,所以需要循环处理每一条
      dbms_output.put_line(v_emp(i).ENAME);
    end loop;
  end loop;
  close cur_emp;
end;
```

#### 5.1.6异常处理

```
--异常处理
--预定义异常（有异常错误信息名称）
```

```
begin
exception 
  when 异常错误信息名称 then --可以写多个
    处理过程;
  when others then
    处理过程;
end;

sqlcode --错误代码
sqlerrm --错误信息

declare
a number;
qq_no number;
```

```
begin
  a:='a';
  select qqno into qq_no from qqinfo where 1=0;
  select qqno into qq_no from qqinfo;
  select 1/0 into a from dual;
exception
  when Zero_divide then
    dbms_output.put_line('除数为零');
  when Too_many_rows then
    dbms_output.put_line('SELECT INTO 返回多行');
  when others then
    dbms_output.put_line(sqlcode||','||sqlerrm); 
end;
```

```
create or replace procedure p1
is
begin
  dbms_output.put_line(1/0);
exception
  when Zero_divide then
    dbms_output.put_line('除数为零');
end;

create or replace procedure p2
is
begin
  dbms_output.put_line('2');
end;
```

```
create or replace procedure p3
is
begin
  dbms_output.put_line('3');
end;

create or replace procedure p4
is
begin
  p1();
  p2();
  p3();
end;

call p4();
```

- 非预定义异常（没有名字能自己判断的错误）

```
异常变量  exception;
PRAGMA EXCEPTION_INIT(异常变量, 异常代码);
begin
exception
  when 异常变量 then
    处理过程;
end;

declare
e1 exception;
PRAGMA EXCEPTION_INIT(e1, -1476);
begin
  dbms_output.put_line(1/0);
exception
  when e1 then 
    dbms_output.put_line('除数为零');
end;
```

- 用户自定义异常处理（逻辑错误）

```
异常变量 exception;
begin
  if ... then--逻辑异常
    raise 异常变量;--抛出异常
  end if;
exception
  when 异常变量 then
    处理过程
end;
```

```
declare
e1 exception;
nl number;
begin
  select age into nl from qqinfo where rownum=1;
  if nl<0 then
    raise e1;--抛出异常
  end if;
exception
  when e1 then
    dbms_output.put_line('年龄过小');
end;

update qqinfo set age = -1 where rownum=1;
```

### 5.2有名快

#### 5.2.1存储过程

- 定义:存储过程相当于存储在数据库中的代码,其实就是匿名块加了一个名字.每次使用时有数据库调入内存中使用;

```
语法:
create or replace procedure 存储过程名字--创建或者替换存储过程,无则创建,有则替换,存储过程的名字一般以proc_开头
as(或者is)--可以理解为 是,相当于 匿名块中的declare
--声名部分
begin
--代码执行部分

exception--异常处理部分
when others then
dbms_output.put_line(SQLERRM);
end;
```

```
范例:   无参数
create or replace procedure proc_test--创建一个名为proc_test的存储过程
as
i number(2):=0;
begin
  loop
    dbms_output.put_line(i);
    exit when i=10;
    i:=i+1;
  end loop;
end;
```

- 存储过程调用方式:

```
1.SQL窗口中call 存储过程名字();--如果存储过程没有参数,也要有括号
2.测试窗口 begin 存储过程名 end;
3.SQLPLUS窗口或者命令行窗口: 
exec 存储过程名;--因为没有输出窗口,所以看不到打印信息;
execute 存储过程名
```

- 存储过程的参数:

- in 参数:传入参数,不能更改:

```
范例:
create or replace procedure proc_add(a in number)--a是参数名字, in是参数类型可以省略，省略默认为in,是传入参数, a不能重新赋值, number是 a 的类型,只有数据类型,没有长度
as
i number(6):=a;
begin
  loop
 dbms_output.put_line(i);
 exit when i=20;
 i:=i+1;
  end loop;
end;
```

```
create or replace procedure proc_add1(a in number)
as
i number(6):=1;
begin
  loop
    dbms_output.put_line(i);
    exit when i=a;
    i:=i+1;
  end loop;
end;
```

- 带in参数存储过程调用:

```
(1):值传递 call proc_test1(3);
(2):参数传递:--使用相对较多
declare
  i number(1):=4;
begin
  proc_test1(i);
end;
(3):按位传递:
begin
  proc_test1(n =>9);--注意n的名字须跟存储过程中参数名字保持一致,使用相对较少
end;
```

- out类型参数:

```
范例:
create or replace procedure proc_out2(s out number)--定义一个名为proc_out2的存储过程,参数名字是s,类型是out,数据类型是number,即这个存储过程会返回一个数字
is
i number(2):=1;--i为循环变量
t number(4):=0;--t是自加求和,此处不能使用s,否则报错重复的变量
begin
loop
  t:=t+i;
  exit when i=5;
  i:=i+1;
  end loop;
  s:=t;--因为返回值是s,所以需要将t的值赋给s
end;
```

```
调用:
declare
a number(3);
begin
  proc_out2(a);
  dbms_output.put_line(a);
end;
```

- in out类型:

```
范例:
create or replace procedure proc_emp( v_emp in out emp%rowtype)--创建存储过程,变量名为v_emp,类型为 in out,说明即是输入参数,也是输出参数,变量的数据类型和emp表保持一致;
as
begin
  select * into v_emp from emp where empno=v_emp.empno;--因为v_emp是输入参数,且类型和emp表一致,所以v_emp.empno就是输入的值, into 后的v_emp是输出类型,返回值;
end;

调用:
declare 
  s emp%rowtype;--定义变量和类型,需要跟存储过程中的参数保持一致;
begin
  s.empno:=7369;--给empno赋初值,此处的s.empno就是存储过程中的v_emp.empno;
  proc_emp(s);--传参执行存储过程
  dbms_output.put_line(s.ename);--因为s是和emp表类型一致的,所以s中也有8列,打印时需要带上列名,否则无法打印;
end;

--参数 两个 类型
create or replace procedure p1(a in out number, b out number)
is
begin
  dbms_output.put_line(a);
  b:=a/2;
end;

调用
declare
b number;
begin
  p1(1,b);
  dbms_output.put_line(b);
  dbms_output.put_line(a);
end;
```

- 参数 in out 类型

```
create or replace procedure p1(a in out number, b out number)
is
begin
  dbms_output.put_line(a);
  b:=a/2;
  a:=a/3;
end;

调用
declare
b number;
a number:=1;
begin
  p1(a,b);
  dbms_output.put_line(b);
  dbms_output.put_line(a);
end;
```

```
存储过程:
create or replace procedure proc_1_1(empno in number, ename in varchar2, job in varchar2, mgr in number, hiredate in date, sal in number, comm in number, deptno in number)
as
begin
insert into emp_0519 values(empno, ename, job, mgr, hiredate, sal, comm, deptno);
commit;  

exception
  when others then
    dbms_output.put_line(SQLERRM);
end;
调用:
declare 
  empno number(4):=4321;
  ename varchar2(30):='AAA';
  job varchar2(30):='ABC';
  mgr number(4):=1234;
  hiredate date:=sysdate-100;
  sal number(10,2):=10.5;
  comm number(10,2):=10.6;
  deptno number(2):=20;
begin
  proc_1_1(empno,ename,job,mgr, hiredate, sal, comm,deptno);
end;
```

```
2.查找出当前用户模式下,每张表的记录数,以scott用户为例,结果应如下：
DEPT...................................4
EMP...................................14
BONUS.................................0
SALGRADE.............................5
```

```
存储过程:
create or replace procedure proc_cnt
as
cursor cur_cnt is select * from user_tables;
v_cnt user_tables%ROWTYPE;
v_sql varchar2(300);
v_count number(10);
begin
  open cur_cnt;
  loop
  fetch cur_cnt into v_cnt;
  exit when cur_cnt%NOTFOUND;
  v_sql:='select count(*) from '||v_cnt.table_name;
  execute immediate v_sql into v_count;
  dbms_output.put_line(v_cnt.table_name||'有'||v_count||'行数据');
  end loop;
  close cur_cnt;
exception
  when others then
    dbms_output.put_line(SQLERRM);
end;

调用:
call proc_cnt();
```

```
3.创建一个过程,能向dept表中添加一个新记录.(in参数)
存储过程:
create or replace procedure proc_3(v_dept in dept%rowtype)
as
begin
  insert into dept values(v_dept.deptno,v_dept.DNAME, v_dept.LOC);
  commit;
exception
  when others then
    dbms_output.put_line(SQLERRM);
end;

调用:
declare 
  v_dept dept%rowtype;
begin
  v_dept.deptno:=60;
  v_dept.dname:='TEST';
  v_dept.loc:='LA';
  proc_3(v_dept);
end;
```

```
4.创建一个过程,从emp表中带入雇员的姓名,返回该雇员的薪水值(out参数);
存储过程:
create or replace procedure proc_4(sal out number)
is
begin
  select sal into sal from emp where ename='SMITH';
exception
  when others then
    dbms_output.put_line(SQLERRM);
end;

调用:
declare 
  sal number(10,4);
begin
  proc_4(sal);
  dbms_output.put_line(sal);
end;
```

```
5.对所有员工,如果该员工职位是MANAGER，并且在DALLAS工作那么就给他薪金加15％；如果该员工职位是CLERK，并且在NEW YORK工作那么就给他薪金扣除5％;其他情况不作处理;
存储过程:
create or replace procedure proc_5
as
begin
  for v in(select a.empno,a.job,b.loc from emp_test a, dept b where a.deptno=b.deptno) loop
    if(v.job='MANAGER' and v.loc='DALLAS') then
    update emp_test set sal=sal*1.15 where empno=v.empno;
    elsif(v.job='CLERK' and v.loc='NEW YORK') then
    update emp_test set sal=sal*0.95 where empno=v.empno;
    end if;
    commit;
  end loop;
end;
调用:
call proc_5();
```

```
6.对直接上级是'BLAKE'的所有员工，按照参加工作的时间加薪：
81年6月以前的加薪10％
81年6月以后的加薪5％

存储过程:
create or replace procedure proc_6
as
begin
  for v in (select empno, hiredate
              from emp_test
             where mgr = (select empno from emp_test where ename = 'BLAKE')) loop
    if (v.hiredate < to_date('19810601', 'YYYYMMDD')) then
      update emp_test set sal = sal * 1.1 where empno = v.empno;
    else
      update emp_test set sal = sal * 1.05 where empno = v.empno;
    end if;
  end loop;
  commit;
end;

调用:
call proc_6();
```

```
7.编写一PL/SQL，对所有的"销售员"(SALESMAN)增加佣金500.
存储过程:
create or replace procedure proc_7
as
begin
  for v in(select * from emp_test where job='SALESMAN')
    loop
      update emp_test set sal=sal+500 where empno=v.empno;
    end loop;
    commit;
end;
调用:
call proc_7();
```

```
8.写一个存储过程，根据输入的参数,修改员工信息，
注：如果只输入员工姓名，那么就只修改姓名
     如果输入多个值，则修改员工的多个信息
     例如：输入员工的姓名、工作、工资，则要求
     把姓名、工作、工资信息都修改
     
存储过程:
create or replace procedure proc_8(v_emp in emp%rowtype)
as
begin
  update emp_test set ename=nvl2(v_emp.ENAME,v_emp.ENAME,ename),
  job=nvl2(v_emp.JOB,v_emp.JOB ,job),
  mgr=nvl2(v_emp.mgr,v_emp.mgr,mgr),
  hiredate=nvl2(v_emp.HIREDATE,v_emp.HIREDATE,hiredate),
  sal=nvl2(v_emp.SAL,v_emp.SAL ,sal),
  comm=nvl2(v_emp.COMM,v_emp.COMM, comm),
  deptno=nvl2(v_emp.DEPTNO,v_emp.DEPTNO ,deptno)
  where empno=v_emp.EMPNO;
  commit;

  exception
    when others then
      dbms_output.put_line(SQLERRM);
end;

调用:
declare 
  v_emp emp%rowtype;
begin
v_emp.EMPNO :=7369;
v_emp.SAL:=3000;
proc_8(v_emp);
end;
```

```
--7.
declare 
begin 
  for i in (select * from emp_0212 where job='SALESMAN')
  LOOP
    for l in (select * from emp_0212) loop
      if i.ename=l.ename then 
        update emp_0212 set  comm=nvl2(comm,comm+500,500) where ename=i.ename;
        end if;
        end loop;
        end loop;
        end;
--6.

declare 
begin 
  for i in (select b.hiredate,b.ename from emp_0212 a, emp_0212 b where a.empno=b.mgr and a.ename='BLAKE') loop 
   if i.hiredate<to_date('19810601','yyyymmdd') then
     update emp_0212 set sal=sal*(1+0.1) where ename=i.ename;
     elsif i.hiredate>to_date('19810601','yyyymmdd') then
     update emp_0212 set sal=sal*(1+0.05) where ename=i.ename;
     end if;
     end loop;
end;
--5.
declare 
begin
  for i in (select a.ename,a.job,b.loc from emp_0212 a, dept1 b where a.deptno=b.deptno) loop
    for l in (select a.ename,a.job,b.loc from emp_0212 a, dept1 b where a.deptno=b.deptno and a.job='MANAGER' and b.loc='DALLAS') loop
      if i.ename=l.ename then
        update emp_0212 set sal=sal*(1+0.15) where ename=l.ename;
        end if;
     for n in(   select a.ename,a.job,b.loc from emp_0212 a, dept1 b where a.deptno=b.deptno and a.job='CLERK' and b.loc='NEW YORK') loop
      if i.ename=n.ename then
        update emp_0212 set sal=sal*(1+0.15) where ename=n.ename;  
        end if ;
        end loop;
        end loop;
        end loop;
end;



---4.
create or replace procedure proc_emp0212(b in emp.ename%type) as

begin
  for i in (select sal from emp_0212 where  ename=b) loop
  dbms_output.put_line(i.sal);
  end loop;
 end;

---3.
create or replace procedure proc_1 (i in dept1%rowtype)
as
c number(30);
begin
  select count(1) into c from dept1 where i.deptno=deptno and i.dname=dname and i.loc=loc;
  if c>0 then
    update dept1 set dname=i.dname,loc=i.loc,deptno=i.deptno where  deptno=i.deptno;
     elsif c=0  then
insert into dept1 values (i.deptno,i.dname,i.loc);
end if ;
end;

declare 
b dept1%rowtype;
begin
  b.deptno:=10;
  b.dname:='aa';
  b.loc:='hongkong';
  proc_1(b); 
  end;

----2.
declare
v_sql varchar2(3000);
b number (30);
begin
  for i in ( select * from user_tables ) loop
    dbms_output.put_line(i.table_name);
    
  v_sql:=q'[ select count(*)  from  ]'|| i.table_name  ;

  execute immediate v_sql into b;
  dbms_output.put_line(i.table_name||'   '||b);

end loop;
end;    
```

#### 5.2.2   函数

- 
  定义:我们之前学习过函数的使用,此处我们说的function就是函数功能的代码实现;

```
语法:
create or replace function 函数名(参数)
return 返回值的数据类型
is

begin

return 变量名;
end;
```

```
范例:
create or replace function func_1(a number, b number)--定义一个函数,名字时func_1,需要输入两个数字类型的参数
return number--返回值是数字类型
is
s number(10,5);
begin
  s:=a+b;
  return s;--将a和b的和S返回,对应之前的return number
end;
```

```
使用:
select func_1(1.1,2.5) from dual;

递归:每次调用自己的方式,叫递归
create or replace function f1(n number)
return number
is  
begin
   if n<=2 then 
     return n;
   else
     return n*f1(n-1);
   end if;
end;
```

- 代码理解:

```
假如我们使用f1(5).5>2,所以执行else分支,返回值是5*f1(4).
f1(4)的值:4大于2,所以执行else分支,f1(4)返回4*f1(3).
f1(3)的值:3大约2,所以执行else分支,f1(3)返回3*f1(2).
f1(2)的值:2=2,执行第一个分支,返回n.

所以f(5)返回: 5*4*3*2.即5的阶乘.
```

#### 5.2.3包

- 包:存储过程,plsql代码,函数等放在一起,形成包;

```
语法: 先执行包头,再执行包体,每个一个文件/窗口;
create or replace package 包名
as
--声明存储过程,函数等
end;

create or replace package body 包名
as
procedure 存储过程名
is
begin
end;

procedure 存储过程名
is
begin
end;

function 函数名
return 数据类型
is
begin
end;

end;
```

```
范例:
create or replace package PKG_1
as
procedure proc_test_1;
procedure proc_test_2(a in number);
procedure proc_test_3;
function function_1(a number) return number;
end;

create or replace package body PKG_1
as

procedure proc_test_1 is
begin
  dbms_output.put_line(1);
end;

procedure proc_test_2(a in number) is
begin
  dbms_output.put_line(a);
end;

procedure proc_test_3 is
begin
  dbms_output.put_line(3);
end;

function function_1(a number) return number is
  s number(10) := a;
begin
  dbms_output.put_line(4);
  return s;
end;

end;
```

- 调用:

```
call PKG_1.proc_test_1();
```

#### 5.2.4触发器

```
触发器:
在执行insert、update、delete语句时，
触发执行的一段plsql代码.可以在sql语句执行前触发
，也可以在sql语句执行后触发，
还可以替换原sql语句只执行触发器代码;

before:代表触发器在语句执行之前触发;
after:代表触发器在语句执行之后触发;
insert or delete or update:代表触发触发器的动作,如果只定义insert,则只有插入时会触发;
begin之后是要执行的代码,即触发触发器后的动作执行部分;
```

- 触发器分类:

```
表级触发器
行级触发器
替换触发器
```

- 表级触发器:一个sql语句只会触发一次触发器代码

```
语法：
create trigger 触发器名称 
before|after insert [or delete [or update]] on 表名

begin

end;
```

```
范例:
create trigger trg_test
before insert or delete or update on trg_test

begin
  dbms_output.put_line(to_char(systimestamp,'YYYY-MM-DD HH24:MI:SS.ff'));
end;
```

```
测试:
insert into trg_test values(to_char(systimestamp,'YYYY-MM-DD HH24:MI:SS.ff'));
```

- 三个属性:

```
inserting:针对插入动作;
deleting:针对删除动作;
updating:针对更新动作;
```

```
范例:
create trigger trg_test1
before insert or delete or update on trg_test

begin
if inserting then
  dbms_output.put_line('inser');
  elsif deleting then
    dbms_output.put_line('delete');
    elsif updating then
      dbms_output.put_line('update');
      end if;
end;
```

```
测试:
insert into trg_test values(to_char(systimestamp,'YYYY-MM-DD HH24:MI:SS.ff'));
delete from trg_test where rownum=1;
update trg_test set date1='TEST';
```

- 行级触发器:针对表中的每一行,数据每操作一行,触发器触发一次;

```
语法:
create trigger 触发器名字
before|ater insert or update or delete on 表名 for each row

begin

end;
```

```
范例:
create trigger trg_test_2
before insert or update or delete on trg_test for each row
begin
  dbms_output.put_line('这是行级触发器');
end;
```

```
测试:
insert into trg_test
select 1 from dual
union all
select 2 from dual
union all
select 3 from dual;
```

- 行级触发器两个属性:(只针对行级触发器)

```
:old :修改之前或者删除之前的旧的数据;
:new :新插入或者即将更新成的数据;
```

```
范例:
create trigger trg_test_3
before insert or update or delete on trg_test for each row

begin
  if updating then
    dbms_output.put_line(:old.date1);
    dbms_output.put_line(:new.date1);
    end if;
end;
```

```
测试:
update trg_test set date1='TEST';
```

- 禁用触发器:

```
alter trigger 触发器名字 disable;
```

- 触发器生效:

```
alter trigger 触发器名字 enable;
```

- 替换触发器:替换触发器只在视图上,不能在表上,用替换触发器时,只执行触发器中的代码,而不执行对视图的操作;

```
语法:
create trigger 触发器名字
instead of insert or delete or update on 视图名 for each row

begin

end;
```

```
范例:
create trigger trg_dept
instead of insert or delete or update on v_dept for each row
begin
  dbms_output.put_line('这是替换触发器');
end;
```

```
调用测试:
insert into v_dept values(22,'CBA','ABC');
select * from v_dept;
```

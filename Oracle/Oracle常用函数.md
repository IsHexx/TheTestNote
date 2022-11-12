# ORACLE函数

#### 执行计划

```sql
#参考文档：https://www.cnblogs.com/mzq123/p/13156390.html
		  ROWID：代表的就是表的数据行所在的物理存储地址

#使用绑定变量方式的是软解析，变量如果写成常量，每次查询时都是硬解析。
#/*+ ALL_ROWS PARALLEL(16)*/
#ORACLE扫描时是从右到左的扫描方式，所以数据量少的条件放右边。
#sql = ("SELECT DBMS_METADATA.GET_DDL('PACKAGE', 'CHEN_JIAN_TEST') FROM USER_OBJECTS") 可打印包、包体、view..... 

```

```sql
【2021年12月25日16:06:54】  Oracle的DBMS_METADATA.GET_DDL()函数详解及扩展
[select DBMS_METADATA.GET_DDL('PACKAGE','PACKAGENAME','USERNAME') from dual]->dbms_metadata.get_ddl->CLOB数据
```



#### **SQL结构化查询语言包含6个部分**

```
dsfsf#1、数据查询语言（DQL: Data Query Language）
#2、数据操纵语言（DML：Data Manipulation Language）
#3、数据定义语言（DDL：Data definition Language）
#4、数据控制语言（DCL：Data Control Language）
#5、事务处理语言（DPL）
#6、指针控制语言（CCL）gyj

SQL结构化查询语言包含6个部分

1.数据查询语言（DQL: Data Query Language）

数据检索语句，用于从表中获取数据。通常最常用的为保留字SELECT,并且常与FROM子句、WHERE子句组成查询SQL查询语句。

语法：

    SELECT <字段名> FROM <表或视图名> WHERE <查询条件>;

2.数据操纵语言（DML：Data Manipulation Language）

sdfsfdssfd语法：

    INSERT INTO <表名>(列1,列2,...) VALUES (值1,值2,...);

    UPDATE <表名> SET <列名>=新值 WHERE <列名>=某值;

    DELETE FROM <表名> WHERE <列名>=某值;

3.事务处理语言（DPL）

事务处理语句能确保被DML语句影响的表的所有行及时得以更新。TPL语句包括BEGIN TRANSACTION、COMMIT和ROLLBACK。

4.数据控制语言（DCL）

通过GRANT和REVOKE，确定单个用户或用户组对数据库对象的访问权限。

5.数据定义语言（DDL）

常用的有CREATE和DROP，用于在数据库中创建新表或删除表，以及为表加入索引等。

6.指针控制语言（CCL）

它的语句，想DECLARE CURSOR、FETCH INTO和UPDATE WHERE CURRENT用于对一个或多个表单独行的操作。


————————————————
版权声明：本文为CSDN博主「小贪玩」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/u013544734/article/details/80869135
```



#### 常用SQL

```sql
SELECT COUNT（1） FROM TABS; --查看共有多少表
SELECT ROUND(SUM(bytes/1024/1024/1024),2) || 'GB' FROM DBA_DATA_FILES; --保留两位小数以GB为单位显示数据文件大小
SELECT * FROM ALL_SOURCE；    --查看当前用户可访问的所有数据库对象的脚本信息
SELECT * FROM DBA_TAB_COLUMNS WHERE COLUMN_NAME = '字段名';    ---查询包含某个字段的表
```

#### ALL_SOURCE

```
ALL_SOURCE，

类型：View

Owner: SYS

内容: 记录了该用户可访问的所有数据库对象的脚本信息(DDL)

字段: 
　　OWNER: 对象的Owner

　　NAME: 对象名称

　　TYPE: 对象类型，如FUNCTION, JAVA SOURCE, PACKAGE, PACKAGE BODY, PROCEDURE, TRIGGER, TYPE, TYPE BODY等

　　LINE: 对象脚本的某一行语句在整个脚本中的行数

　　TEXT: 对象脚本的某一行语句

　　ORIGIN_CON_ID: 数据来源的容器的ID
```

#### Replace

```
例：

select filefullname from sys_frmattachmentdb
查询的结果为：
e:\GengBaoFile\TYGW\《历城区项目立项审批流程》.1079\\3186.通用流程项目资料.jpg

需求：
要将结果中的“历城区”修改为"北京区"。


操作：
使用的函数为replace()

含义为：替换字符串
replace(原字段，“原字段旧内容“,“原字段新内容“,)

语句：
update sys_frmattachmentdb  set filefullname = replace(filefullname,'历城区,'北京区)
————————————————
原文链接：https://blog.csdn.net/hugaozhuang/article/details/37600637
```

#### ROWNUM

```
Oracle中rownum的基本用法
对于rownum来说它是oracle系统顺序分配为从查询返回的行的编号，返回的第一行分配的是1，第二行是2，依此类推，这个伪字段可以用于限制查询返回的总行数，且rownum不能以任何表的名称作为前缀。

(1) rownum 对于等于某值的查询条件
如果希望找到学生表中第一条学生的信息，可以使用rownum=1作为条件。但是想找到学生表中第二条学生的信息，使用rownum=2结果查不到数据。因为rownum都是从1开始，但是1以上的自然数在rownum做等于判断是时认为都是false条件，所以无法查到rownum = n（n>1的自然数）。
SQL> select rownum,id,name from student where rownum=1;（可以用在限制返回记录条数的地方，保证不出错，如：隐式游标）
SQL> select rownum,id,name from student where rownum =2; 
    ROWNUM ID     NAME
---------- ------ ---------------------------------------------------

（2）rownum对于大于某值的查询条件
   如果想找到从第二行记录以后的记录，当使用rownum>2是查不出记录的，原因是由于rownum是一个总是从1开始的伪列，Oracle 认为rownum> n(n>1的自然数)这种条件依旧不成立，所以查不到记录。

查找到第二行以后的记录可使用以下的子查询方法来解决。注意子查询中的rownum必须要有别名，否则还是不会查出记录来，这是因为rownum不是某个表的列，如果不起别名的话，无法知道rownum是子查询的列还是主查询的列。
SQL>select * from(select rownum no ,id,name from student) where no>2;
        NO ID     NAME
---------- ------ ---------------------------------------------------
         3 200003 李三
         4 200004 赵四

（3）rownum对于小于某值的查询条件
rownum对于rownum<n（(n>1的自然数）的条件认为是成立的，所以可以找到记录。
SQL> select rownum,id,name from student where rownum <3;
    ROWNUM ID     NAME
---------- ------ ---------------------------------------------------
        1 200001 张一
        2 200002 王二

查询rownum在某区间的数据，必须使用子查询。例如要查询rownum在第二行到第三行之间的数据，包括第二行和第三行数据，那么我们只能写以下语句，先让它返回小于等于三的记录行，然后在主查询中判断新的rownum的别名列大于等于二的记录行。但是这样的操作会在大数据集中影响速度。
SQL> select * from (select rownum no,id,name from student where rownum<=3 ) where no >=2;
        NO ID     NAME
---------- ------ ---------------------------------------------------
         2 200002 王二
         3 200003 李三

（4）rownum和排序   
Oracle中的rownum的是在取数据的时候产生的序号，所以想对指定排序的数据去指定的rowmun行数据就必须注意了。
SQL> select rownum ,id,name from student order by name;
    ROWNUM ID     NAME
---------- ------ ---------------------------------------------------
         3 200003 李三
         2 200002 王二
         1 200001 张一
         4 200004 赵四
可以看出，rownum并不是按照name列来生成的序号。系统是按照记录插入时的顺序给记录排的号，rowid也是顺序分配的。为了解决这个问题，必须使用子查询；
SQL> select rownum ,id,name from (select * from student order by name);
    ROWNUM ID     NAME
---------- ------ ---------------------------------------------------
         1 200003 李三
         2 200002 王二
         3 200001 张一
         4 200004 赵四
这样就成了按name排序，并且用rownum标出正确序号（有小到大）
笔者在工作中有一上百万条记录的表，在jsp页面中需对该表进行分页显示，便考虑用rownum来作，下面是具体方法(每页显示20条)： 
“select * from tabname where rownum<20 order by name" 但却发现oracle却不能按自己的意愿来执行，而是先随便取20条记录，然后再order by，后经咨询oracle,说rownum确实就这样，想用的话，只能用子查询来实现先排序，后rownum，方法如下： 
"select * from (select * from tabname order by name) where rownum<20",但这样一来，效率会低很多。 
后经笔者试验，只需在order by 的字段上加主键或索引即可让oracle先按该字段排序，然后再rownum；方法不变：    “select * from tabname where rownum<20 order by name"

取得某列中第N大的行

select column_name from 
(select table_name.*,dense_rank() over (order by column desc) rank from table_name) 
where rank = &N； 
　假如要返回前5条记录：

　　select * from tablename where rownum<6;(或是rownum <= 5 或是rownum != 6) 
假如要返回第5-9条记录：

select * from tablename 
where … 
and rownum<10 
minus 
select * from tablename 
where … 
and rownum<5 
order by name 
选出结果后用name排序显示结果。(先选再排序)

注意：只能用以上符号(<、<=、!=)。

select * from tablename where rownum != 10;返回的是前９条记录。 
不能用：>,>=,=,Between...and。由于rownum是一个总是从1开始的伪列，Oracle 认为这种条件不成立。

另外，这个方法更快：

select * from ( 
select rownum r,a from yourtable 
where rownum <= 20 
order by name ) 
where r > 10 
这样取出第11-20条记录!(先选再排序再选)

要先排序再选则须用select嵌套：内层排序外层选。 
rownum是随着结果集生成的，一旦生成，就不会变化了；同时,生成的结果是依次递加的，没有1就永远不会有2! 
rownum 是在查询集合产生的过程中产生的伪列，并且如果where条件中存在 rownum 条件的话，则:

1： 假如判定条件是常量，则： 
只能 rownum = 1, <= 大于1 的自然数， = 大于1 的数是没有结果的；大于一个数也是没有结果的 
即 当出现一个 rownum 不满足条件的时候则 查询结束 this is stop key（一个不满足，系统将该记录过滤掉，则下一条记录的rownum还是这个，所以后面的就不再有满足记录，this is stop key）；

2： 假如判定值不是常量，则：

若条件是 = var , 则只有当 var 为1 的时候才满足条件，这个时候不存在 stop key ,必须进行full scan ,对每个满足其他where条件的数据进行判定，选出一行后才能去选rownum=2的行……
```

查询数据库中的空表

```sql
SQL> DECLARE

v_table tabs.table_name%TYPE;

v_sql VARCHAR2(888);

v_q NUMBER;

CURSOR c1 IS

SELECT table_name tn FROM tabs;

TYPE c IS REF CURSOR;

c2 c;

BEGIN

DBMS_OUTPUT.PUT_LINE('以下为空数据表的表名:');

FOR r1 IN c1 LOOP

v_table :=r1.tn;

v_sql :='SELECT count(*) q FROM '||v_table||' where rownum = 1';

OPEN c2 FOR v_sql;

LOOP

FETCH c2 INTO v_q;

EXIT WHEN c2%NOTFOUND;

IF v_q=0 THEN

DBMS_OUTPUT.PUT_LINE(v_table);

END IF;

END LOOP;

CLOSE c2;

END LOOP;

EXCEPTION

WHEN OTHERS THEN DBMS_OUTPUT.PUT_LINE('Error occurred');

END;

/

PL/SQL 过程已成功完成。

SQL> set serveroutput on

SQL> /

以下为空数据表的表名:

T_FILE_INFO_RAW

T_DOSSIER_INFO_RAW

T_FONDS_INFO_RAW
```

#### CRON表达式

```CRON
Cron表达式是一个字符串，字符串以5或6个空格隔开，分为6或7个域，每一个域代表一个含义，Cron有如下两种语法格式：

　　（1） Seconds Minutes Hours DayofMonth Month DayofWeek Year

　　（2）Seconds Minutes Hours DayofMonth Month DayofWeek

　　

　　一、结构

　　corn从左到右（用空格隔开）：秒 分 小时 月份中的日期 月份 星期中的日期 年份

　　二、各字段的含义

 
字段	允许值	允许的特殊字符
秒（Seconds）	0~59的整数	, - * /    四个字符
分（Minutes）	0~59的整数	, - * /    四个字符
小时（Hours）	0~23的整数	, - * /    四个字符
日期（DayofMonth）	1~31的整数（但是你需要考虑你月的天数）	,- * ? / L W C     八个字符
月份（Month）	1~12的整数或者 JAN-DEC	, - * /    四个字符
星期（DayofWeek）	1~7的整数或者 SUN-SAT （1=SUN）	, - * ? / L C #     八个字符
年(可选，留空)（Year）	1970~2099	, - * /    四个字符
 

 
　　注意事项：

　　每一个域都使用数字，但还可以出现如下特殊字符，它们的含义是：

　　（1）*：表示匹配该域的任意值。假如在Minutes域使用*, 即表示每分钟都会触发事件。

　　（2）?：只能用在DayofMonth和DayofWeek两个域。它也匹配域的任意值，但实际不会。因为DayofMonth和DayofWeek会相互影响。例如想在每月的20日触发调度，不管20日到底是星期几，则只能使用如下写法： 13 13 15 20 * ?, 其中最后一位只能用？，而不能使用*，如果使用*表示不管星期几都会触发，实际上并不是这样。

　　（3）-：表示范围。例如在Minutes域使用5-20，表示从5分到20分钟每分钟触发一次 

　　（4）/：表示起始时间开始触发，然后每隔固定时间触发一次。例如在Minutes域使用5/20,则意味着5分钟触发一次，而25，45等分别触发一次. 

　　（5）,：表示列出枚举值。例如：在Minutes域使用5,20，则意味着在5和20分每分钟触发一次。 

　　（6）L：表示最后，只能出现在DayofWeek和DayofMonth域。如果在DayofWeek域使用5L,意味着在最后的一个星期四触发。 

　　（7）W:表示有效工作日(周一到周五),只能出现在DayofMonth域，系统将在离指定日期的最近的有效工作日触发事件。例如：在 DayofMonth使用5W，如果5日是星期六，则将在最近的工作日：星期五，即4日触发。如果5日是星期天，则在6日(周一)触发；如果5日在星期一到星期五中的一天，则就在5日触发。另外一点，W的最近寻找不会跨过月份 。

　　（8）LW:这两个字符可以连用，表示在某个月最后一个工作日，即最后一个星期五。 

　　（9）#:用于确定每个月第几个星期几，只能出现在DayofMonth域。例如在4#2，表示某月的第二个星期三。
```

#### JOB相关

```
select * from dba_jobs_running;   查看正在运行的JOB
SELECT SID,JOB FROM DBA_JOBS_RUNNING; 找出正在执行的JOB编号及其会话编号
```

#### WHERE EXISTS

```
首先 where exists 中会做父表的遍历和对子表的查询（尽管这里的对子表的遍历，应该是只符合条件就会返回，并不一定会完全遍历完子表）。如果在父表小，子表大的情况下，这种写法的效率会很高，并且 t4.deptno=emp.deptno，是可以走索引的。效率不会很差。但是如果父表很大的情况下，这种效率就不会很高。因为要对父表进行遍历（全表扫描）。
而in 的等价替换中的(select distinct deptno from t4)，如果t4 这个表很小的情况下，效率也是非常快的。但是这个语句在 t4 很大的情况下效率是非常低的。首先 oracle 会先挂起 父查询的语句，先去将子查询执行完毕后，再进行关联查询。这时候，如果 父表很大而子表很小，效率就会比 where exists 高。
总的来说，in 和 where exists 在两个表想当的情况下，效率应该是差不多的。
但是如果在父表大子表小的情况下 in 的效率要比 where exists快。
相反如果是在子表大而父表小的情况下这时候where exists 的效率就要比in快了。
```

#### with as 的用法详解

```sql
一般查询语句，我们会使用select串接好数据来查询，如果是很复杂是，还可能写个视图来查，有没有那种查询复杂的数据后，还要继续做筛选的，又不想去写个视图再操作的方法呢？oracle为我们提供了with的写法，很好的解决了这个问题

with t1 as (select * form table_name ),  --这里相当于t是一个临时表
     t2 as (select * from table_name1)  
select * form t;                       --这里就可以直接对t数据进行查询操作

```


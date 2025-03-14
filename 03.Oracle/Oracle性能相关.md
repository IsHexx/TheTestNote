# Oracle性能相关

## 1.相关概念

### **Rowid的概念**

```
rowid是一个伪列，既然是伪列，那么这个列就不是用户定义，而是系统自己给加上的。 对每个表都有一个rowid的伪列，但是表中并不物理存储ROWID列的值。不过你可以像使用其它列那样使用它，但是不能删除改列，也不能对该列的值进行 修改、插入。一旦一行数据插入数据库，则rowid在该行的生命周期内是唯一的，即即使该行产生行迁移，行的rowid也不会改变。
```

### **Recursive SQL概念**

```
有时为了执行用户发出的一个sql语句，Oracle必须执行一些额外的语句，我们将这些额外的语句称之为''recursive calls''或''recursive SQL statements''.如当一个DDL语句发出后，ORACLE总是隐含的发出一些recursive SQL语句，来修改数据字典信息，以便用户可以成功的执行该DDL语句。当需要的数据字典信息没有在共享内存中时，经常会发生Recursive calls，这些Recursive calls会将数据字典信息从硬盘读入内存中。用户不比关心这些recursive SQL语句的执行情况，在需要的时候，ORACLE会自动的在内部执行这些语句。当然DML语句与SELECT都可能引起recursive SQL.简单的说，我们可以将触发器视为recursive SQL.
```

### **Row Source（行源)**

```
用在查询中，由上一操作返回的符合条件的行的集合，即可以是表的全部行数据的集合；也可以是表的部分行数据的集合；也可以为对上2个row source进行连接操作（如join连接）后得到的行数据集合
```

### **Predicate（谓词）**

```
一个查询中的WHERE限制条件
```

### **Driving Table（驱动表）**

```
该表又称为外层表（OUTER TABLE）。这个概念用于嵌套与HASH连接中。如果该row source返回较多的行数据，则对所有的后续操作有负面影响。注意此处虽然翻译为驱动表，但实际上翻译为驱动行源（driving row source）更为确切。一般说来，是应用查询的限制条件后，返回较少行源的表作为驱动表，所以如果一个大表在WHERE条件有有限制条件（如等值限 制），则该大表作为驱动表也是合适的，所以并不是只有较小的表可以作为驱动表，正确说法应该为应用查询的限制条件后，返回较少行源的表作为驱动表。在执行 计划中，应该为靠上的那个row source，后面会给出具体说明。在我们后面的描述中，一般将该表称为连接操作的row source 1. 
```

### **Probed Table（被探查表)**

```
该表又称为内层表（INNER TABLE）。在我们从驱动表中得到具体一行的数据后，在该表中寻找符合连接条件的行。所以该表应当为大表（实际上应该为返回较大row source的表）且相应的列上应该有索引。在我们后面的描述中，一般将该表称为连接操作的row source 2.
```

### **组合索引（concatenated index）**

```
由多个列构成的索引，如create index idx_emp on emp（col1， col2， col3， ……），则我们称idx_emp索引为组合索引。在组合索引中有一个重要的概念：引导列（leading column），在上面的例子中，col1列为引导列。当我们进行查询时可以使用“where col1 = ？ ”，也可以使用“where col1 = ？ and col2 = ？”，这样的限制条件都会使用索引，但是“where col2 = ？ ”查询就不会使用该索引。所以限制条件中包含先导列时，该限制条件才会使用该组合索引
```

### **可选择性（selectivity）**

```
比较一下列中唯一键的数量和表中的行数，就可以判断该列的可选择性。 如果该列的“唯一键的数量/表中的行数”的比值越接近1，则该列的可选择性越高，该列就越适合创建索引，同样索引的可选择性也越高。在可选择性高的列上进 行查询时，返回的数据就较少，比较适合使用索引查询。
```

###  **全表扫描（Full Table Scans， FTS）**

```
为实现全表扫描，Oracle读取表中所有的行，并检查每一行是否满足语句的WHERE限制条件一个多块读操作可以使一次I/O能读取多块数据块（db_block_multiblock_read_count参数设定），而不是只读取一个数据块，这极大的减 少了I/O总次数，提高了系统的吞吐量，所以利用多块读的方法可以十分高效地实现全表扫描，而且只有在全表扫描的情况下才能使用多块读操作。在这种访问模 式下，每个数据块只被读一次。
　　使用FTS的前提条件：在较大的表上不建议使用全表扫描，除非取出数据的比较多，超过总量的5% —— 10%，或你想使用并行查询功能时。
　　使用全表扫描的例子： 
　　SQL> explain plan for select * from dual;
　　Query Plan
　　-----------------------------------------
　　SELECT STATEMENT[CHOOSE] Cost=
　　TABLE ACCESS FULL DUAL
```

### **通过ROWID的表存取（Table Access by ROWID或rowid lookup）**

```
行的ROWID指出了该行所在的数据文件、数据块以及行在该块中的位置，所以通过ROWID来存取数据可以快速定位到目标数据上，是Oracle存取单行数据的最快方法。
　　这种存取方法不会用到多块读操作，一次I/O只能读取一个数据块。我们会经常在执行计划中看到该存取方法，如通过索引查询数据。
　　使用ROWID存取的方法： 
　　SQL> explain plan for select * from dept where rowid = ''AAAAyGAADAAAAATAAF''；
 
　　Query Plan
　　------------------------------------
　　SELECT STATEMENT [CHOOSE] Cost=1
　　TABLE ACCESS BY ROWID DEPT [ANALYZED]
```

### **索引扫描（Index Scan或index lookup）**

```
　我们先通过index查找到数据对应的rowid值（对于非唯一索引可能返回多个rowid值），然后根据rowid直接从表中得到具体的数据，这 种查找方式称为索引扫描或索引查找（index lookup）。一个rowid唯一的表示一行数据，该行对应的数据块是通过一次i/o得到的，在此情况下该次i/o只会读取一个数据库块。
　　在索引中，除了存储每个索引的值外，索引还存储具有此值的行对应的ROWID值。
　　索引扫描可以由2步组成：
　　（1） 扫描索引得到对应的rowid值。
　　（2） 通过找到的rowid从表中读出具体的数据。
　　每步都是单独的一次I/O，但是对于索引，由于经常使用，绝大多数都已经CACHE到内存中，所以第1步的 I/O经常是逻辑I/O，即数据可以从内存中得到。但是对于第2步来说，如果表比较大，则其数据不可能全在内存中，所以其I/O很有可能是物理I/O，这 是一个机械操作，相对逻辑I/O来说，是极其费时间的。所以如果多大表进行索引扫描，取出的数据如果大于总量的5% —— 10%，使用索引扫描会效率下降很多。如下列所示：
　　SQL> explain plan for select empno， ename from emp where empno=10；
　　Query Plan
　　------------------------------------
　　SELECT STATEMENT [CHOOSE] Cost=1
　　TABLE ACCESS BY ROWID EMP [ANALYZED]
　　INDEX UNIQUE SCAN EMP_I1

　　但是如果查询的数据能全在索引中找到，就可以避免进行第2步操作，避免了不必要的I/O，此时即使通过索引扫描取出的数据比较多，效率还是很高的
　　SQL> explain plan for select empno from emp where empno=10;-- 只查询empno列值
　　Query Plan
　　------------------------------------
　　SELECT STATEMENT [CHOOSE] Cost=1
　　INDEX UNIQUE SCAN EMP_I1

　　进一步讲，如果sql语句中对索引列进行排序，因为索引已经预先排序好了，所以在执行计划中不需要再对索引列进行排序
　　SQL> explain plan for select empno, ename from emp
　　where empno > 7876 order by empno;
　　Query Plan
　　--------------------------------------------------------------------------------
　　SELECT STATEMENT[CHOOSE] Cost=1
　　TABLE ACCESS BY ROWID EMP [ANALYZED]
　　INDEX RANGE SCAN EMP_I1 [ANALYZED]

　　从这个例子中可以看到：因为索引是已经排序了的，所以将按照索引的顺序查询出符合条件的行，因此避免了进一步排序操作。
```

### **根据索引的类型与where限制条件的不同，有4种类型的索引扫描**

```
索引唯一扫描（index unique scan）
索引范围扫描（index range scan）
索引全扫描（index full scan）
索引快速扫描（index fast full scan）
```

### **索引唯一扫描（index unique scan）**

```
通过唯一索引查找一个数值经常返回单个ROWID.如果存在UNIQUE 或PRIMARY KEY 约束（它保证了语句只存取单行）的话，Oracle经常实现唯一性扫描。
　　使用唯一性约束的例子：
　　SQL> explain plan for
　　select empno，ename from emp where empno=10；
　　Query Plan
　　------------------------------------
　　SELECT STATEMENT [CHOOSE] Cost=1
　　TABLE ACCESS BY ROWID EMP [ANALYZED]
　　INDEX UNIQUE SCAN EMP_I1
```

### **索引范围扫描（index range scan）**

```
使用一个索引存取多行数据，在唯一索引上使用索引范围扫描的典型情况下是在谓词（where限制条件）中使用了范围操作符（如>、<、<>、>=、<=、between）
　　使用索引范围扫描的例子：
　　SQL> explain plan for select empno，ename from emp
　　where empno > 7876 order by empno；
　　Query Plan
　　--------------------------------------------------------------------------------
　　SELECT STATEMENT[CHOOSE] Cost=1
　　TABLE ACCESS BY ROWID EMP [ANALYZED]
　　INDEX RANGE SCAN EMP_I1 [ANALYZED]

　　在非唯一索引上，谓词col = 5可能返回多行数据，所以在非唯一索引上都使用索引范围扫描。
　　使用index rang scan的3种情况：
　　（a） 在唯一索引列上使用了range操作符（> < <> >= <= between）
　　（b） 在组合索引上，只使用部分列进行查询，导致查询出多行
　　（c） 对非唯一索引列上进行的任何查询。
```

### **索引全扫描（index full scan）**

```
　与全表扫描对应，也有相应的全索引扫描。而且此时查询出的数据都必须从索引中可以直接得到。
　　全索引扫描的例子：
　　An Index full scan will not perform single block i/o''s and so it may prove to be inefficient.
　　e.g.
　　Index BE_IX is a concatenated index on big_emp （empno， ename）
　　SQL> explain plan for select empno， ename from big_emp order by empno，ename；
　　Query Plan
　　--------------------------------------------------------------------------------
　　SELECT STATEMENT[CHOOSE] Cost=26
　　INDEX FULL SCAN BE_IX [ANALYZED]
```

### **索引快速扫描（index fast full scan）**

```
扫描索引中的所有的数据块，与 index full scan很类似，但是一个显著的区别就是它不对查询出的数据进行排序，即数据不是以排序顺序被返回。在这种存取方法中，可以使用多块读功能，也可以使用并行读入，以便获得最大吞吐量与缩短执行时间。
　　索引快速扫描的例子：
　　BE_IX索引是一个多列索引： big_emp （empno，ename）
　　SQL> explain plan for select empno，ename from big_emp；
　　Query Plan
　　------------------------------------------
　　SELECT STATEMENT[CHOOSE] Cost=1
　　INDEX FAST FULL SCAN BE_IX [ANALYZED]

　　只选择多列索引的第2列：
　　SQL> explain plan for select ename from big_emp；
　　Query Plan
　　------------------------------------------
　　SELECT STATEMENT[CHOOSE] Cost=1
　　INDEX FAST FULL SCAN BE_IX [ANALYZED]
```

### **表之间的连接**

```
　Join是一种试图将两个表结合在一起的谓词，一次只能连接2个表，表连接也可以被称为表关联。在后面的叙 述中，我们将会使用“row source”来代替“表”，因为使用row source更严谨一些，并且将参与连接的2个row source分别称为row source1和row source 2.Join过程的各个步骤经常是串行操作，即使相关的row source可以被并行访问，即可以并行的读取做join连接的两个row source的数据，但是在将表中符合限制条件的数据读入到内存形成row source后，join的其它步骤一般是串行的。有多种方法可以将2个表连接起来，当然每种方法都有自己的优缺点，每种连接类型只有在特定的条件下才会 发挥出其最大优势。
　　row source（表）之间的连接顺序对于查询的效率有非常大的影响。通过首先存取特定的表，即将该表作为驱动表，这样可以先应用某些限制条件，从而得到一个 较小的row source，使连接的效率较高，这也就是我们常说的要先执行限制条件的原因。一般是在将表读入内存时，应用where子句中对该表的限制条件。
　　根据2个row source的连接条件的中操作符的不同，可以将连接分为等值连接（如WHERE A.COL3 = B.COL4）、非等值连接（WHERE A.COL3 > B.COL4）、外连接（WHERE A.COL3 = B.COL4（+））。上面的各个连接的连接原理都基本一样，所以为了简单期间，下面以等值连接为例进行介绍。
　　在后面的介绍中，都以以下Sql为例进行说明：
　　SELECT A.COL1， B.COL2
　　FROM A， B
　　WHERE A.COL3 = B.COL4；
　　假设A表为Row Soruce1，则其对应的连接操作关联列为COL 3；
　　B表为Row Soruce2，则其对应的连接操作关联列为COL 4；

　　连接类型：
　　目前为止，无论连接操作符如何，典型的连接类型共有3种：
　　排序 - - 合并连接（Sort Merge Join （SMJ） ）
　　嵌套循环（Nested Loops （NL） ）
　　哈希连接（Hash Join）
　　另外，还有一种Cartesian product（笛卡尔积），一般情况下，尽量避免使用。
```

### **排序 - - 合并连接（Sort Merge Join， SMJ）**

```
内部连接过程：
　　1） 首先生成row source1需要的数据，然后对这些数据按照连接操作关联列（如A.col3）进行排序。
　　2） 随后生成row source2需要的数据，然后对这些数据按照与sort source1对应的连接操作关联列（如B.col4）进行排序。
　　3） 最后两边已排序的行被放在一起执行合并操作，即将2个row source按照连接条件连接起来

　　下面是连接步骤的图形表示：
　　MERGE
　　/\
　　SORTSORT
　　||
　　Row Source 1Row Source 2

　　如果row source已经在连接关联列上被排序，则该连接操作就不需要再进行sort操作，这样可以大大提高这种连接操作的连接速度，因为排序是个极其费资源的操 作，特别是对于较大的表。预先排序的row source包括已经被索引的列（如a.col3或b.col4上有索引）或row source已经在前面的步骤中被排序了。尽管合并两个row source的过程是串行的，但是可以并行访问这两个row source（如并行读入数据，并行排序）。
　　SMJ连接的例子：
　　SQL> explain plan for
　　select /*+ ordered */ e.deptno， d.deptno
　　from emp e， dept d
　　where e.deptno = d.deptno
　　order by e.deptno， d.deptno；
　　Query Plan
　　-------------------------------------
　　SELECT STATEMENT [CHOOSE] Cost=17
　　MERGE JOIN
　　SORT JOIN
　　TABLE ACCESS FULL EMP [ANALYZED]
　　SORT JOIN
　　TABLE ACCESS FULL DEPT [ANALYZED]

　　排序是一个费时、费资源的操作，特别对于大表。基于这个原因，SMJ经常不是一个特别有效的连接方法，但是如果2个row source都已经预先排序，则这种连接方法的效率也是比较高的一种方式。
```

### **嵌套循环（Nested Loops， NL）**

```
这个连接方法有驱动表（外部表）的概念。其实，该连接过程就是一个2层嵌套循环，所以外层循环的次数越少越好，这也就是我们为什么将小表或返回较小 row source的表作为驱动表（用于外层循环）的理论依据。但是这个理论只是一般指导原则，因为遵循这个理论并不能总保证使语句产生的I/O次数最少。有时 不遵守这个理论依据，反而会获得更好的效率。如果使用这种方法，决定使用哪个表作为驱动表很重要。有时如果驱动表选择不正确，将会导致语句的性能很差、很差。
　　内部连接过程：
　　Row source1的Row 1 —— Probe ->Row source 2
　　Row source1的Row 2 —— Probe ->Row source 2
　　Row source1的Row 3 —— Probe ->Row source 2
　　……。
　　Row source1的Row n —— Probe ->Row source 2

　　从内部连接过程来看，需要用row source1中的每一行，去匹配row source2中的所有行，所以此时保持row source1尽可能的小与高效的访问row source2（一般通过索引实现）是影响这个连接效率的关键问题。这只是理论指导原则，目的是使整个连接操作产生最少的物理I/O次数，而且如果遵守这 个原则，一般也会使总的物理I/O数最少。但是如果不遵从这个指导原则，反而能用更少的物理I/O实现连接操作，那尽管违反指导原则吧！因为最少的物理 I/O次数才是我们应该遵从的真正的指导原则，在后面的具体案例分析中就给出这样的例子。
　　在上面的连接过程中，我们称Row source1为驱动表或外部表。Row Source2被称为被探查表或内部表。
　　在NESTED LOOPS连接中，Oracle读取row source1中的每一行，然后在row sourc2中检查是否有匹配的行，所有被匹配的行都被放到结果集中，然后处理row source1中的下一行。这个过程一直继续，直到row source1中的所有行都被处理。这是从连接操作中可以得到第一个匹配行的最快的方法之一，这种类型的连接可以用在需要快速响应的语句中，以响应速度为 主要目标。
　　如果driving row source（外部表）比较小，并且在inner row source（内部表）上有唯一索引，或有高选择性非唯一索引时，使用这种方法可以得到较好的效率。NESTED LOOPS有其它连接方法没有的的一个优点是：可以先返回已经连接的行，而不必等待所有的连接操作处理完才返回数据，这可以实现快速的响应时间。
　　如果不使用并行操作，最好的驱动表是那些应用了where 限制条件后，可以返回较少行数据的的表，所以大表也可能称为驱动表，关键看限制条件。对于并行查询，我们经常选择大表作为驱动表，因为大表可以充分利用并 行功能。当然，有时对查询使用并行操作并不一定会比查询不使用并行操作效率高，因为最后可能每个表只有很少的行符合限制条件，而且还要看你的硬件配置是否 可以支持并行（如是否有多个CPU，多个硬盘控制器），所以要具体问题具体对待。
　　NL连接的例子：
　　SQL> explain plan for
　　select a.dname，b.sql
　　from dept a，emp b
　　where a.deptno = b.deptno；
　　Query Plan
　　-------------------------
　　SELECT STATEMENT [CHOOSE] Cost=5
　　NESTED LOOPS
　　TABLE ACCESS FULL DEPT [ANALYZED]
　　TABLE ACCESS FULL EMP [ANALYZED]
```

### **哈希连接（Hash Join， HJ）**

```
这种连接是在oracle 7.3以后引入的，从理论上来说比NL与SMJ更高效，而且只用在CBO优化器中。
　　较小的row source被用来构建hash table与bitmap，第2个row source被用来被hansed，并与第一个row source生成的hash table进行匹配，以便进行进一步的连接。Bitmap被用来作为一种比较快的查找方法，来检查在hash table中是否有匹配的行。特别的，当hash table比较大而不能全部容纳在内存中时，这种查找方法更为有用。这种连接方法也有NL连接中所谓的驱动表的概念，被构建为hash table与bitmap的表为驱动表，当被构建的hash table与bitmap能被容纳在内存中时，这种连接方式的效率极高。

　　HASH连接的例子：
　　SQL> explain plan for
　　select /*+ use_hash（emp） */ empno
　　from emp， dept
　　where emp.deptno = dept.deptno；
　　Query Plan
　　----------------------------
　　SELECT STATEMENT[CHOOSE] Cost=3
　　HASH JOIN
　　TABLE ACCESS FULL DEPT
　　TABLE ACCESS FULL EMP

　　要使哈希连接有效，需要设置HASH_JOIN_ENABLED=TRUE，缺省情况下该参数为TRUE，另外，不要忘了还要设置 hash_area_size参数，以使哈希连接高效运行，因为哈希连接会在该参数指定大小的内存中运行，过小的参数会使哈希连接的性能比其他连接方式还 要低。

　　另外，笛卡儿乘积（Cartesian Product）
　　当两个row source做连接，但是它们之间没有关联条件时，就会在两个row source中做笛卡儿乘积，这通常由编写代码疏漏造成（即程序员忘了写关联条件）。笛卡尔乘积是一个表的每一行依次与另一个表中的所有行匹配。在特殊情况下我们可以使用笛卡儿乘积，如在星形连接中，除此之外，我们要尽量不使用笛卡儿乘积，否则，自己想结果是什么吧！
　　注意在下面的语句中，在2个表之间没有连接。
　　SQL> explain plan for
　　select emp.deptno，dept，deptno
　　from emp，dept
　　Query Plan
　　------------------------
　　SLECT STATEMENT [CHOOSE] Cost=5
　　MERGE JOIN CARTESIAN
　　TABLE ACCESS FULL DEPT
　　SORT JOIN
　　TABLE ACCESS FULL EMP

　　CARTESIAN关键字指出了在2个表之间做笛卡尔乘积。假如表emp有n行，dept表有m行，笛卡尔乘积的结果就是得到n * m行结果。

```

### **连接方法比较**

```
排序 - - 合并连接（Sort Merge Join， SMJ）：
　　a） 对于非等值连接，这种连接方式的效率是比较高的。
　　b） 如果在关联的列上都有索引，效果更好。
　　c） 对于将2个较大的row source做连接，该连接方法比NL连接要好一些。
　　d） 但是如果sort merge返回的row source过大，则又会导致使用过多的rowid在表中查询数据时，数据库性能下降，因为过多的I/O.

　　嵌套循环（Nested Loops， NL）：
　　a） 如果driving row source（外部表）比较小，并且在inner row source（内部表）上有唯一索引，或有高选择性非唯一索引时，使用这种方法可以得到较好的效率。
　　b） NESTED LOOPS有其它连接方法没有的的一个优点是：可以先返回已经连接的行，而不必等待所有的连接操作处理完才返回数据，这可以实现快速的响应时间。

　　哈希连接（Hash Join， HJ）：
　　a） 这种方法是在oracle7后来引入的，使用了比较先进的连接理论，一般来说，其效率应该好于其它2种连接，但是这种连接只能用在CBO优化器中，而且需要设置合适的hash_area_size参数，才能取得较好的性能。
　　b） 在2个较大的row source之间连接时会取得相对较好的效率，在一个row source较小时则能取得更好的效率。
　　c） 只能用于等值连接中
```



## 2.Sql执行顺序及原理

### 2.1执行原理

- 简要执行顺序：语法分析> 语义分析> 视图转换 >表达式转换> 选择优化器 >选择连接方式 >选择连接顺序 >选择数据的搜索路径 >运行“执行计划”

- 第一步：'客户端' 把语句发给 '服务器端' 执行（'用户进程' -> '服务器进程'）(连接器)

```

(1) Oracle '客户端'(如：pl/sql Developer) 是不会做任何操作的，它的主要任务就是把 '客户端' 产生的 sql 语句发送给 '服务端'
(2) Oracle '服务端' 才会对sql语句处理
(3) '客户端' 的进程跟 '服务器端' 的进程是 "一对一" 
```

- 第二步：语句解析(查询缓存)

```
(1) 查询 '库缓存（librart cache）'，是否存在 '相同语句的执行计划',
    a）如果存在，服务器进程会直接进入语句执行阶段（直接到第三步省略后续步骤，提高查询效率（软解析）
    B）如果不存在，服务器进程会从硬盘中读取 '数据文件'，继续后续步骤（硬解析）
(2) '语法检查'：是否合乎语法规则
(3) '语义检查'：字段、表等内容是否在数据库中
(4) 获得 '对象解析锁'：当 语法语义都正确后，系统会对我们需要查询的对象加锁。
	这主要是为了保证数据的一致性（'事务特性之一'），防止我们在查询过程中，其他用户对该对象的结构发生改变
(5) '权限检查'：该用户是否拥有权限访问这些数据
(6) 确定 '最佳执行计划'：Oracle 数据库对 SQL 语句自我优化
```

- 第三步：语句执行(分析器->优化器->执行器)

```
对于 select：
	(1)'服务器进程' 要判断所需数据是否在 '库缓存（librart cache）'
	    a) 如果存在，则直接获取该数据而不是从 '数据库文件' 中去查询数据，同时，根据 LRU（Least 				Recently Used 最近最少使用）算法增加其访问次数
	 	b) 如果不存在，则 '服务器进程' 将从 '数据库文件' 中查询相关数据并且，将这些数据放入到 '库缓存			librart cache'中
```

```
对于 update、insert、delete：
	(1) 检查 sql 是否已经读取到 '库缓存（librart cache）'
	    如果已存在，则直接执行步骤 3
	(2) 如果不存在，则 '服务器进程' 将 '数据块' 从 '数据文件' 读取到 '库缓存'
	(3) 对想要修改的表取得数据的行锁定（Row Exclusive Lock）
	(4) 将数据的 Redo 记录复制到 '重做日志缓冲区 Redo Log Buffer'
	(5) 产生修改的 undo 数据
	(6) 修改 '库缓存（librart cache）'
	(7) 后台进程 dbwr 将修改写入 '数据文件'
```

- 第四步：提取数据	

```
使用到的相关试图
	select * from v$session t;
	select * from v$sql t;
```

### 2.2执行顺序

- sql查询语法结构

```
 SELECT DISTINCT <..>
   FROM <..> JOIN <..> ON <..>
  WHERE <..>
  GROUP BY <..>
 HAVING <..>
  ORDER BY <..>
```

- sql处理顺序

```
1. FROM --首位，sql入口
2. ON
3. JOIN
4. WHERE
5. GROUP BY
6. HAVING 
7. SELECT
8. DISTINCT
9. ORDER BY
```

- 整体：从内到外，从下到上，从右到左

```
实际使用过程中， 除了上述的基本结构外还会有多种嵌套的SQL，它们遵循的执行顺序是“从内到外，从下到上，从右到左”，可通过执行计划看到Sql执行顺序
（1）pl/sql Developer中使用F5查看执行计划
（2）通过dbms_xplan，执行以下SQL
    explain plan for 
        --这里是你需要查看执行计划的SQL语句
    commit;
    select * from table(dbms_xplan.display);
```

## 3.性能优化

### 3.1常用优化规则

**1.SELECT子句中避免使用“*”**

```
Oracle在解析SQL语句的时候，对于“*”将通过查询数据库字典来将其转换成对应的列名。
如果在Select子句中需要列出所有的Column时，建议列出所有的Column名称，而不是简单的用“*”来替代，这样可以减少多于的数据库查询开销。
```

**2.用EXISTS替代IN**

```
在许多基于基础表的查询中，为了满足一个条件 ，往往需要对另一个表进行联接。在这种情况下，使用EXISTS(或NOT EXISTS)通常将提高查询的效率
```

**3.用EXISTS替换DISTINCT**

```
当提交一个包含对多表信息（比如部门表和雇员表）的查询时，避免在SELECT子句中使用DISTINCT。 一般可以考虑用EXIST替换。

EXISTS 使查询更为迅速，因为RDBMS核心模块将在子查询的条件一旦满足后，立刻返回结果。
```

**4.用 >= 替代 >**

```
如果DEPTNO上有一个索引

高效:
	SELECT *
     FROM EMP
   WHERE DEPTNO >=4
   
低效：
	SELECT *
     FROM EMP
   WHERE DEPTNO >3
   
两者的区别在于，前者DBMS将直接跳到第一个DEPT等于4的记录，而后者将首先定位到DEPTNO等于3的记录并且向前扫描到第一个DEPT大于3的记录.   
```

**5.用Union替换OR（适用于索引列）**

```
通常情况下，用UNION替换WHERE子句中的OR将会起到较好的效果。对索引列使用OR将造成全表扫描。 注意，以上规则只针对多个索引列有效。
```

**6.用IN替换OR**

**7.使用UNION ALL替代UNION**

```
当SQL语句需要UNION两个查询结果集合时，这两个结果集合会以UNION-ALL的方式被合并，然后在输出最终结果前进行排序。如果用UNION ALL替代UNION，这样排序就不是必要了，效率就会因此得到提高。

由于UNION ALL的结果没有经过排序，而且不过滤重复的记录，因此是否进行替换需要根据业务需求而定。
```

**8.使用提示（Hints）**

```
FULL hint 告诉ORACLE使用全表扫描的方式访问指定表。
ROWID hint 告诉ORACLE使用TABLE ACCESS BY ROWID的操作访问表。
CACHE hint 来告诉优化器把查询结果数据保留在SGA中。
INDEX Hint 告诉ORACLE使用基于索引的扫描方式。

其他的Oracle Hints
    ALL_ROWS
    FIRST_ROWS
    RULE
    USE_NL
    USE_MERGE
    USE_HASH 
```

**9.使用日期**

```
当使用日期时，需要注意如果有超过5位小数加到日期上，这个日期会进到下一天!
```

**10.选择最有效率的表名顺序**

```
ORACLE的解析器按照从右到左的顺序处理FROM子句中的表名，因此FROM子句中写在最后的表(基础表 driving table)将被最先处理。
当ORACLE处理多个表时，会运用排序及合并的方式连接它们。首先，扫描第一个表(FROM子句中最后的那个表)并对记录进行派序，然后扫描第二个表(FROM子句中最后第二个表)，最后将所有从第二个表中检索出的记录与第一个表中合适记录进行合并。
只在基于规则的优化器中有效。
```

**11.可以过滤掉最大数量记录的条件必须写在WHERE子句的末尾**

```
Oracle采用自下而上的顺序解析WHERE子句。 根据这个原理,表之间的连接必须写在其他WHERE条件之前，那些可以过滤掉最大数量记录的条件必须写在WHERE子句的末尾。
```

**12.用Where子句替换Having子句**

```
避免使用HAVING子句，HAVING 只会在检索出所有记录之后才对结果集进行过滤。这个处理需要排序、总计等操作。 如果能通过WHERE子句限制记录的数目，就能减少这方面的开销。
```

**13.在Where子句中引用取别名的列**

```
可以使用子查询方式先过滤出需要的列

范例：
	Select * 
		From （Select name，age From epm） x
	Where age<50;
```

**14.计算记录条数**

```
Select count(*) from tablename; 
Select count(1) from tablename; 
Select count(column) from tablename;

一般认为，在没有主键索引的情况之下，第二种COUNT(1)方式最快。如果只有一列且无索引COUNT(*)反而比较快， 如果有索引列，当然是使用索引列COUNT(column)最快。
```

**15.识别低效的SQL语句下面的SQL工具可以找出低效SQL，前提是需要DBA权限**

```
SELECT EXECUTIONS, DISK_READS, BUFFER_GETS,
ROUND ((BUFFER_GETS-DISK_READS)/BUFFER_GETS, 2) Hit_radio,
ROUND (DISK_READS/EXECUTIONS, 2) Reads_per_run,
   SQL_TEXT
FROM   V$SQLAREA
WHERE  EXECUTIONS>0
AND  BUFFER_GETS > 0 
AND (BUFFER_GETS-DISK_READS)/BUFFER_GETS < 0.8
ORDER BY 4 DESC;
```

### 3.2索引

- 特点：提高效率 主键的唯一性验证代价，需要空间存储 定期维护重构索引

**1.Oracle对索引的四种访问模式**

```
索引唯一扫描（index unique scan）
索引范围扫描（index range scan）
索引全扫描（index full scan）
索引快速扫描（index fast full scan）
```

**2.基础表的选择**

```
基础表(Driving Table)是指被最先访问的表(通常以全表扫描的方式被访问)。根据优化器的不同，SQL语句中基础表的选择是不一样的。
如果你使用的是CBO (COST BASED OPTIMIZER)，优化器会检查SQL语句中的每个表的物理大小，索引的状态，然后选用花费最低的执行路径。
如果你用RBO (RULE BASED OPTIMIZER)， 并且所有的连接条件都有索引对应，在这种情况下，基础表就是FROM 子句中列在最后的那个表。
```

**3.多个平等的索引**

```
当SQL语句的执行路径可以使用分布在多个表上的多个索引时，ORACLE会同时使用多个索引并在运行时对它们的记录进行合并，检索出仅对全部索引有效的记录。
在ORACLE选择执行路径时，唯一性索引的等级高于非唯一性索引。然而这个规则只有当WHERE子句中索引列和常量比较才有效。如果索引列和其他表的索引类相比较。这种子句在优化器中的等级是非常低的。
如果不同表中两个相同等级的索引将被引用，FROM子句中表的顺序将决定哪个会被率先使用。FROM子句中最后的表的索引将有最高的优先级。
如果相同表中两个相同等级的索引将被引用，WHERE子句中最先被引用的索引将有最高的优先级。
```

**4.等式比较优先于范围比较DEPTNO上有一个非唯一性索引，EMP_CAT也有一个非唯一性索引**

```
SELECT ENAME FROM EMP
WHERE DEPTNO > 20
AND EMP_CAT = 'A'

这里只有EMP_CAT索引被用到,然后所有的记录将逐条与DEPTNO条件进行比较. 执行路径如下:
TABLE ACCESS BY ROWID ON EMP
INDEX RANGE SCAN ON CAT_IDX

即使是唯一性索引，如果做范围比较，其优先级也低于非唯一性索引的等式比较。
```

**5.不明确的索引等级当ORACLE无法判断索引的等级高低差别，优化器将只使用一个索引,它就是在WHERE子句中被列在最前面的。DEPTNO上有一个非唯一性索引，EMP_CAT也有一个非唯一性索引。**

```
SELECT ENAME FROM EMP
WHERE DEPTNO > 20
AND EMP_CAT > 'A'

这里, ORACLE只用到了DEPT_NO索引. 执行路径如下:
TABLE ACCESS BY ROWID ON EMP
INDEX RANGE SCAN ON DEPT_IDX
```

**6.强制索引失效如果两个或以上索引具有相同的等级，你可以强制命令ORACLE优化器使用其中的一个(通过它,检索出的记录数量少)** 

```
SELECT ENAME
FROM EMP
WHERE EMPNO = 7935
AND DEPTNO + 0 = 10    /*DEPTNO上的索引将失效*/
AND EMP_TYPE || '' = 'A'  /*EMP_TYPE上的索引将失效*/
```

**7.避免在索引列上使用计算WHERE子句中，如果索引列是函数的一部分。优化器将不使用索引而使用全表扫描**

```
/*低效SQL*/
SELECT * FROM DEPT
WHERE SAL * 12 > 25000;

/*高效SQL*/
SELECT * FROM DEPT
WHERE SAL > 25000/12;
```

**8.自动选择索引如果表中有两个以上（包括两个）索引，其中有一个唯一性索引，而其他是非唯一性索引。在这种情况下，ORACLE将使用唯一性索引而完全忽略非唯一性索引**

```
SELECT ENAME FROM EMP 
WHERE EMPNO = 2326
AND DEPTNO = 20;

这里，只有EMPNO上的索引是唯一性的，所以EMPNO索引将用来检索记录。
SELECT ENAME FROM EMP 
WHERE EMPNO = 2326
AND DEPTNO = 20;
```

**9.避免在索引列上使用NOT通常，我们要避免在索引列上使用NOT，NOT会产生在和在索引列上使用函数相同的影响。当ORACLE遇到NOT，它就会停止使用索引转而执行全表扫描**

```
/*低效SQL: (这里，不使用索引)*/
SELECT * FROM DEPT
WHERE NOT DEPT_CODE = 0

/*高效SQL: (这里，使用索引)*/
SELECT * FROM DEPT
WHERE DEPT_CODE > 0
```

**10.总是使用索引的第一个列如果索引是建立在多个列上， 只有在它的第一个列(leading column)被where子句引用时， 优化器才会选择使用该索引**

```
SQL> create index multindex on multiindexusage(inda,indb);
Index created.

SQL> select * from  multiindexusage where indb = 1;
Execution Plan
----------------------------------------------------------
     SELECT STATEMENT Optimizer=CHOOSE
0   TABLE ACCESS (FULL) OF 'MULTIINDEXUSAGE‘

很明显, 当仅引用索引的第二个列时,优化器使用了全表扫描而忽略了索引。
```

**11.避免改变索引列的类型**

```
当比较不同数据类型的数据时， ORACLE自动对列进行简单的类型转换。
/*假设EMP_TYPE是一个字符类型的索引列.*/
SELECT *
FROM EMP
WHERE EMP_TYPE = 123

/*这个语句被ORACLE转换为:*/
SELECT *
FROM EMP
WHERE TO_NUMBER(EMP_TYPE)=123

因为内部发生的类型转换，这个索引将不会被用到。几点注意：
    a)当比较不同数据类型的数据时，ORACLE自动对列进行简单的类型转换。
    b)如果在索引列上面进行了隐式类型转换，在查询的时候将不会用到索引。
    c)注意当字符和数值比较时，ORACLE会优先转换数值类型到字符类型。
    d)为了避免ORACLE对SQL进行隐式的类型转换，最好把类型转换用显式表现出来。
```

**12.几种不能使用索引的WHERE子句****

```
!=
‘||’
+
```

**13.CBO下使用更具选择性的索引**

```
a)基于成本的优化器（CBO，Cost-Based Optimizer）对索引的选择性进行判断来决定索引的使用是否能提高效率。
b)如果检索数据量超过30%的表中记录数，使用索引将没有显著的效率提高。
c)在特定情况下，使用索引也许会比全表扫描慢。而通常情况下，使用索引比全表扫描要块几倍乃至几千倍！
```

**14.分离表和索引**

```
a)总是将你的表和索引建立在不同的表空间内（TABLESPACES）。
b)决不要将不属于ORACLE内部系统的对象存放到SYSTEM表空间里。
c)确保数据表空间和索引表空间置于不同的硬盘上
```

### 3.3执行计划

#### **执行计划常用列字段解释**

```
基数（Rows）：Oracle估计的当前操作的返回结果集行数

字节（Bytes）：执行该步骤后返回的字节数

耗费（COST）、CPU耗费：Oracle估计的该步骤的执行成本，用于说明SQL执行的代价，理论上越小越好（该值可能与实际有出入）

时间（Time）：Oracle估计的当前操作所需的时间
```

#### **什么是执行计划**

```
执行计划是一条查询语句在Oracle中的执行过程或访问路径的描述
```

#### **怎样查看Oracle执行计划？**

```
两个方法：
（1）pl/sql Developer中使用F5查看执行计划
（2）通过dbms_xplan，执行以下SQL
    explain plan for 
        --这里是你需要查看执行计划的SQL语句
    commit;
    select * from table(dbms_xplan.display);
```

![img](http://images2015.cnblogs.com/blog/946400/201611/946400-20161118093807045-1069484935.png)

```
1）：执行顺序：

根据Operation缩进来判断，缩进最多的最先执行；（缩进相同时，最上面的最先执行）

例：上图中 INDEX RANGE SCAN 和 INDEX UNIQUE SCAN 两个动作缩进最多，最上面的 INDEX RANGE SCAN 先执行；

同一级如果某个动作没有子ID就最先执行

同一级的动作执行时遵循最上最右先执行的原则

例：上图中 TABLE ACCESS BY GLOBAL INDEX ROWID 和 TABLE ACCESS BY INDEX ROWID 两个动作缩进都在同一级，则位于上面的 TABLE ACCESS BY GLOBAL INDEX ROWID 这个动作先执行；这个动作又包含一个子动作 INDEX RANGE SCAN，则位于右边的子动作 INDEX RANGE SCAN 先执行；

图示中的SQL执行顺序即为：

INDEX RANGE SCAN  —>  TABLE ACCESS BY GLOBAL INDEX ROWID  —>  INDEX UNIQUE SCAN  —>  TABLE ACCESS BY INDEX ROWID  —>  NESTED LOOPS OUTER  —>  SORT GROUP BY  —>  SELECT STATEMENT, GOAL = ALL_ROWS

（ 注：PLSQL提供了查看执行顺序的功能按钮(上图中的红框部分) ）
```

```
对图中动作的一些说明：

1. 上图中 TABLE ACCESS BY …  即描述的是该动作执行时表访问（或者说Oracle访问数据）的方式；

表访问的几种方式：（非全部）

TABLE ACCESS FULL（全表扫描）
TABLE ACCESS BY ROWID（通过ROWID的表存取）
TABLE ACCESS BY INDEX SCAN（索引扫描）
（1） TABLE ACCESS FULL（全表扫描）：

Oracle会读取表中所有的行，并检查每一行是否满足SQL语句中的 Where 限制条件；

全表扫描时可以使用多块读（即一次I/O读取多块数据块）操作，提升吞吐量；

使用建议：数据量太大的表不建议使用全表扫描，除非本身需要取出的数据较多，占到表数据总量的 5% ~ 10% 或以上

（2） TABLE ACCESS BY ROWID（通过ROWID的表存取） :

先说一下什么是ROWID？

rowid

ROWID是由Oracle自动加在表中每行最后的一列伪列，既然是伪列，就说明表中并不会物理存储ROWID的值；

你可以像使用其它列一样使用它，只是不能对该列的值进行增、删、改操作；

一旦一行数据插入后，则其对应的ROWID在该行的生命周期内是唯一的，即使发生行迁移，该行的ROWID值也不变。

让我们再回到 TABLE ACCESS BY ROWID 来：

行的ROWID指出了该行所在的数据文件、数据块以及行在该块中的位置，所以通过ROWID可以快速定位到目标数据上，这也是Oracle中存取单行数据最快的方法；

（3）上图中的 NESTED LOOPS … 描述的是表连接方式；
JOIN 关键字用于将两张表作连接，一次只能连接两张表，JOIN 操作的各步骤一般是串行的（在读取做连接的两张表的数据时可以并行读取）；

表（row source）之间的连接顺序对于查询效率有很大的影响，对首先存取的表（驱动表）先应用某些限制条件（Where过滤条件）以得到一个较小的row source，可以使得连接效率提高。

（4）上图中的 … OUTER 描述的是表连接类型；
        表连接的两种类型：
            INNER JOIN（内连接）
            OUTER JOIN（外连接）
（5） TABLE ACCESS BY INDEX SCAN（索引扫描）：

在索引块中，既存储每个索引的键值，也存储具有该键值的行的ROWID。

一个数字列上建索引后该索引可能的概念结构如下图：
```

![img](http://images2015.cnblogs.com/blog/946400/201611/946400-20161118093809670-597482185.png)

```
所以索引扫描其实分为两步：

Ⅰ：扫描索引得到对应的ROWID

Ⅱ：通过ROWID定位到具体的行读取数据
```

**索引扫描延伸**

```
INDEX UNIQUE SCAN（索引唯一扫描）
INDEX RANGE SCAN（索引范围扫描）
INDEX FULL SCAN（索引全扫描）
INDEX FAST FULL SCAN（索引快速扫描）
INDEX SKIP SCAN（索引跳跃扫描）
```

 **INDEX UNIQUE SCAN（索引唯一扫描）**

```
针对唯一性索引（UNIQUE INDEX）的扫描，每次至多只返回一条记录；
表中某字段存在 UNIQUE、PRIMARY KEY 约束时，Oracle常实现唯一性扫描；
```

**INDEX RANGE SCAN（索引范围扫描）**

```
使用一个索引存取多行数据；

发生索引范围扫描的三种情况：

在唯一索引列上使用了范围操作符（如：>   <   <>   >=   <=   between）
在组合索引上，只使用部分列进行查询（查询时必须包含前导列，否则会走全表扫描）
```

**INDEX FULL SCAN（索引全扫描）**

```
进行全索引扫描时，查询出的数据都必须从索引中可以直接得到（注意全索引扫描只有在CBO模式下才有效）
```

**INDEX FAST FULL SCAN（索引快速扫描）**

```
扫描索引中的所有的数据块，与 INDEX FULL SCAN 类似，但是一个显著的区别是它不对查询出的数据进行排序（即数据不是以排序顺序被返回）
```

**INDEX SKIP SCAN（索引跳跃扫描）**

```
Oracle 9i后提供，有时候复合索引的前导列（索引包含的第一列）没有在查询语句中出现，oralce也会使用该复合索引，这时候就使用的INDEX SKIP SCAN;

什么时候会触发 INDEX SKIP SCAN 呢？

前提条件：表有一个复合索引，且在查询时有除了前导列（索引中第一列）外的其他列作为条件，并且优化器模式为CBO时

当Oracle发现前导列的唯一值个数很少时，会将每个唯一值都作为常规扫描的入口，在此基础上做一次查找，最后合并这些查询；

例如：

假设表emp有ename（雇员名称）、job（职位名）、sex（性别）三个字段，并且建立了如 create index idx_emp on emp (sex, ename, job) 的复合索引；

因为性别只有 '男' 和 '女' 两个值，所以为了提高索引的利用率，Oracle可将这个复合索引拆成 ('男', ename, job)，('女', ename, job) 这两个复合索引；

当查询 select * from emp where job = 'Programmer' 时，该查询发出后：

Oracle先进入sex为'男'的入口，这时候使用到了 ('男', ename, job) 这条复合索引，查找 job = 'Programmer' 的条目；

再进入sex为'女'的入口，这时候使用到了 ('女', ename, job) 这条复合索引，查找 job = 'Programmer' 的条目；

最后合并查询到的来自两个入口的结果集。
```

**Oracle优化器**

```
Oracle中的优化器是SQL分析和执行的优化工具，它负责生成、制定SQL的执行计划。

Oracle的优化器有两种：

RBO（Rule-Based Optimization） 基于规则的优化器
CBO（Cost-Based Optimization） 基于代价的优化器
RBO：

RBO有严格的使用规则，只要按照这套规则去写SQL语句，无论数据表中的内容怎样，也不会影响到你的执行计划；

换句话说，RBO对数据“不敏感”，它要求SQL编写人员必须要了解各项细则；

RBO一直沿用至ORACLE 9i，从ORACLE 10g开始，RBO已经彻底被抛弃。

CBO：

CBO是一种比RBO更加合理、可靠的优化器，在ORACLE 10g中完全取代RBO；

CBO通过计算各种可能的执行计划的“代价”，即COST，从中选用COST最低的执行方案作为实际运行方案；

它依赖数据库对象的统计信息，统计信息的准确与否会影响CBO做出最优的选择，也就是对数据“敏感”。
```

**驱动表（Driving Table）与匹配表（Probed Table）**

```
驱动表（Driving Table）：

表连接时首先存取的表，又称外层表（Outer Table），这个概念用于 NESTED LOOPS（嵌套循环） 与 HASH JOIN（哈希连接）中；

如果驱动表返回较多的行数据，则对所有的后续操作有负面影响，故一般选择小表（应用Where限制条件后返回较少行数的表）作为驱动表。

匹配表（Probed Table）：

又称为内层表（Inner Table），从驱动表获取一行具体数据后，会到该表中寻找符合连接条件的行。故该表一般为大表（应用Where限制条件后返回较多行数的表）。
```

**表连接的几种方式**

```
SORT MERGE JOIN（排序-合并连接）
NESTED LOOPS（嵌套循环）
HASH JOIN（哈希连接）
CARTESIAN PRODUCT（笛卡尔积）

注：这里将首先存取的表称作 row source 1，将之后参与连接的表称作 row source 2；
```

**SORT MERGE JOIN（排序-合并连接）**：

```
假设有查询：select a.name, b.name from table_A a join table_B b on (a.id = b.id)

内部连接过程：

a) 生成 row source 1 需要的数据，按照连接操作关联列（如示例中的a.id）对这些数据进行排序

b) 生成 row source 2 需要的数据，按照与 a) 中对应的连接操作关联列（b.id）对数据进行排序

c) 两边已排序的行放在一起执行合并操作（对两边的数据集进行扫描并判断是否连接）

延伸：

如果示例中的连接操作关联列 a.id，b.id 之前就已经被排过序了的话，连接速度便可大大提高，因为排序是很费时间和资源的操作，尤其对于有大量数据的表。

故可以考虑在 a.id，b.id 上建立索引让其能预先排好序。不过遗憾的是，由于返回的结果集中包括所有字段，所以通常的执行计划中，即使连接列存在索引，也不会进入到执行计划中，除非进行一些特定列处理（如仅仅只查询有索引的列等）。

排序-合并连接的表无驱动顺序，谁在前面都可以；

排序-合并连接适用的连接条件有： <   <=   =   >   >= ，不适用的连接条件有： <>    like
```

**NESTED LOOPS（嵌套循环）**：

```
内部连接过程：

a) 取出 row source 1 的 row 1（第一行数据），遍历 row source 2 的所有行并检查是否有匹配的，取出匹配的行放入结果集中

b) 取出 row source 1 的 row 2（第二行数据），遍历 row source 2 的所有行并检查是否有匹配的，取出匹配的行放入结果集中

c) ……

若 row source 1 （即驱动表）中返回了 N 行数据，则 row source 2 也相应的会被全表遍历 N 次。

因为 row source 1 的每一行都会去匹配 row source 2 的所有行，所以当 row source 1 返回的行数尽可能少并且能高效访问 row source 2（如建立适当的索引）时，效率较高。

延伸：

嵌套循环的表有驱动顺序，注意选择合适的驱动表。

嵌套循环连接有一个其他连接方式没有的好处是：可以先返回已经连接的行，而不必等所有的连接操作处理完才返回数据，这样可以实现快速响应。

应尽可能使用限制条件（Where过滤条件）使驱动表（row source 1）返回的行数尽可能少，同时在匹配表（row source 2）的连接操作关联列上建立唯一索引（UNIQUE INDEX）或是选择性较好的非唯一索引，此时嵌套循环连接的执行效率会变得很高。若驱动表返回的行数较多，即使匹配表连接操作关联列上存在索引，连接效率也不会很高。
```

**HASH JOIN（哈希连接）** :

```
哈希连接只适用于等值连接（即连接条件为  =  ）

HASH JOIN对两个表做连接时并不一定是都进行全表扫描，其并不限制表访问方式；

内部连接过程简述：

a) 取出 row source 1（驱动表，在HASH JOIN中又称为Build Table） 的数据集，然后将其构建成内存中的一个 Hash Table（Hash函数的Hash KEY就是连接操作关联列），创建Hash位图（bitmap）

b) 取出 row source 2（匹配表）的数据集，对其中的每一条数据的连接操作关联列使用相同的Hash函数并找到对应的 a) 里的数据在 Hash Table 中的位置，在该位置上检查能否找到匹配的数据
```

**Hash Table相关**

```
来自Wiki的解释：

In computing, a hash table (hash map) is a data structure used to implement an associative array, a structure that can map keys to values. A hash table uses a hash function to compute an index into an array of buckets or slots, from which the desired value can be found.

散列（hash）技术：在记录的存储位置和记录具有的关键字key之间建立一个对应关系 f ，使得输入key后，可以得到对应的存储位置 f(key)，这个对应关系 f 就是散列（哈希）函数；

采用散列技术将记录存储在一块连续的存储空间中，这块连续的存储空间就是散列表（哈希表）；

 不同的key经同一散列函数散列后得到的散列值理论上应该不同，但是实际中有可能相同，相同时即是发生了散列（哈希）冲突，解决散列冲突的办法有很多，比如HashMap中就是用链地址法来解决哈希冲突；

哈希表是一种面向查找的数据结构，在输入给定值后查找给定值对应的记录在表中的位置以获取特定记录这个过程的速度很快。
```

**HASH JOIN的三种模式：**

```
OPTIMAL HASH JOIN
ONEPASS HASH JOIN
MULTIPASS HASH JOIN
```

**OPTIMAL HASH JOIN**：

```
OPTIMAL 模式是从驱动表（也称Build Table）上获取的结果集比较小，可以把根据结果集构建的整个Hash Table都建立在用户可以使用的内存区域里。
```

![img](http://images2015.cnblogs.com/blog/946400/201611/946400-20161118093811263-1663864103.jpg)

```
连接过程简述：

Ⅰ：首先对Build Table内各行数据的连接操作关联列使用Hash函数，把Build Table的结果集构建成内存中的Hash Table。如图所示，可以把Hash Table看作内存中的一块大的方形区域，里面有很多的小格子，Build Table里的数据就分散分布在这些小格子中，而这些小格子就是Hash Bucket（见上面Wiki的定义）。

Ⅱ：开始读取匹配表（Probed Table）的数据，对其中每行数据的连接操作关联列都使用同上的Hash函数，定位Build Table里使用Hash函数后具有相同值数据所在的Hash Bucket。

Ⅲ：定位到具体的Hash Bucket后，先检查Bucket里是否有数据，没有的话就马上丢掉匹配表（Probed Table）的这一行。如果里面有数据，则继续检查里面的数据（驱动表的数据）是否和匹配表的数据相匹配。
```

**ONEPASS HASH JOIN** 

```
从驱动表（也称Build Table）上获取的结果集较大，无法将根据结果集构建的Hash Table全部放入内存中时，会使用 ONEPASS 模式。
```

![img](http://images2015.cnblogs.com/blog/946400/201611/946400-20161118093812467-1244869778.jpg)

```
连接过程简述：

Ⅰ：对Build Table内各行数据的连接操作关联列使用Hash函数，根据Build Table的结果集构建Hash Table后，由于内存无法放下所有的Hash Table内容，将导致有的Hash Bucket放在内存里，有的Hash Bucket放在磁盘上，无论放在内存里还是磁盘里，Oracle都使用一个Bitmap结构来反映这些Hash Bucket的状态（包括其位置和是否有数据）。

Ⅱ：读取匹配表数据并对每行的连接操作关联列使用同上的Hash函数，定位Bitmap上Build Table里使用Hash函数后具有相同值数据所在的Bucket。如果该Bucket为空，则丢弃匹配表的这条数据。如果不为空，则需要看该Bucket是在内存里还是在磁盘上。

如果在内存中，就直接访问这个Bucket并检查其中的数据是否匹配，有匹配的话就返回这条查询结果。

如果在磁盘上，就先把这条待匹配数据放到一边，将其先暂存在内存里，等以后积累了一定量的这样的待匹配数据后，再批量的把这些数据写入到磁盘上（上图中的 Dump probe partitions to disk）。

Ⅲ：当把匹配表完整的扫描了一遍后，可能已经返回了一部分匹配的数据了。接下来还有Hash Table中一部分在磁盘上的Hash Bucket数据以及匹配表中部分被写入到磁盘上的待匹配数据未处理，现在Oracle会把磁盘上的这两部分数据重新匹配一次，然后返回最终的查询结果。
```

**MULTIPASS HASH JOIN**：

```
当内存特别小或者相对而言Hash Table的数据特别大时，会使用 MULTIPASS 模式。MULTIPASS会多次读取磁盘数据，应尽量避免使用该模式。
```

表连接的两种类型：

```
INNER JOIN（内连接）
OUTER JOIN（外连接）
```

示例数据说明：

现有A、B两表，A表信息如下：

![img](http://images2015.cnblogs.com/blog/946400/201611/946400-20161118093813810-1888018472.png)

B表信息如下：

![img](http://images2015.cnblogs.com/blog/946400/201611/946400-20161118093814607-1147302494.png)

下面的例子都用A、B两表来演示

**（1） **INNER JOIN（内连接）**：

```
只返回两表中相匹配的记录。

INNER JOIN 又分为两种：

等值连接（连接条件为  = ）
非等值连接（连接条件为 非 =  ，如  >  >=  <  <=  等）
等值连接用的最多，下面以等值连接举例：

内连接的两种写法：

Ⅰ： select a.id A_ID, a.name A_NAME, b.id B_ID, b.name B_NAME from A a inner join B b on (a.id = b.id)

Ⅱ： select a.id A_ID, a.name A_NAME, b.id B_ID, b.name B_NAME from A a join B b on (a.id = b.id)

连接时只返回满足连接条件（a.id = b.id）的记录：

```

![img](http://images2015.cnblogs.com/blog/946400/201611/946400-20161118093815467-1196607555.png)

**（2） **OUTER JOIN（外连接）**

```
OUTER JOIN 分为三种：

LEFT OUTER JOIN（可简写为 LEFT JOIN，左外连接）
RIGHT OUTER JOIN（ RIGHT JOIN，右外连接）
FULL OUTER JOIN（ FULL JOIN，全外连接）
```

**a) **LEFT JOIN（左连接）**：**

```
返回的结果不仅包含符合连接条件的记录，还包含左边表中的全部记录。（若返回的左表中某行记录在右表中没有匹配项，则右表中的返回列均为空值）

两种写法：

Ⅰ：select a.id A_ID, a.name A_NAME, b.id B_ID, b.name B_NAME from A a left outer join B b on (a.id = b.id)

Ⅱ：select a.id A_ID, a.name A_NAME, b.id B_ID, b.name B_NAME from A a left join B b on (a.id = b.id)

返回结果：
```

![img](http://images2015.cnblogs.com/blog/946400/201611/946400-20161118093816248-850734151.png)

**b) **RIGHT JOIN（右连接）**：

```
返回的结果不仅包含符合连接条件的记录，还包含右边表中的全部记录。（若返回的右表中某行记录在左表中没有匹配项，则左表中的返回列均为空值）

两种写法：

Ⅰ：select a.id A_ID, a.name A_NAME, b.id B_ID, b.name B_NAME from A a right outer join B b on (a.id = b.id)

Ⅱ：select a.id A_ID, a.name A_NAME, b.id B_ID, b.name B_NAME from A a right join B b on (a.id = b.id)

返回结果：
```

![img](http://images2015.cnblogs.com/blog/946400/201611/946400-20161118093817201-814601926.png)

**c) **FULL JOIN（全连接）**：

```
返回左右两表的全部记录。(左右两边不匹配的项都以空值代替)

两种写法：

Ⅰ：select a.id A_ID, a.name A_NAME, b.id B_ID, b.name B_NAME from A a full outer join B b on (a.id = b.id)

Ⅱ：select a.id A_ID, a.name A_NAME, b.id B_ID, b.name B_NAME from A a full join B b on (a.id = b.id)

返回结果：
```

![img](http://images2015.cnblogs.com/blog/946400/201611/946400-20161118093818045-1711684549.png)

 **(+) 操作符**

```
(+) 操作符是Oracle特有的表示法，用来表示外连接（只能表示 左外、右外 连接），需要配合Where语句使用。

特别注意：**(+) 操作符在左表的连接条件上表示右连接，在右表的连接条件上表示左连接**。

如：
```

```
Ⅰ：select a.id A_ID, a.name A_NAME, b.id B_ID, b.name B_NAME from A a, B b where a.id = b.id(+)

查询结果：
```

![img](http://images2015.cnblogs.com/blog/946400/201611/946400-20161118093818795-2109864572.png)

```
实际与左连接 select a.id A_ID, a.name A_NAME, b.id B_ID, b.name B_NAME from A a left join B b on (a.id = b.id) 效果等价
```

```
Ⅱ：select a.id A_ID, a.name A_NAME, b.id B_ID, b.name B_NAME from A a, B b where a.id(+) = b.id

查询结果：
```

![img](http://images2015.cnblogs.com/blog/946400/201611/946400-20161118093819592-1404230827.png)

```
实际与右连接 select a.id A_ID, a.name A_NAME, b.id B_ID, b.name B_NAME from A a right join B b on (a.id = b.id) 效果等价
```


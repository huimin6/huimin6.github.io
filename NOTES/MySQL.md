<!-- MarkdownTOC -->

- [MySQL](#mysql)
- [MySQL架构](#mysql架构)
- [SQL语句](#sql语句)
- [事务](#事务)
- [索引](#索引)
  + [InnoDB 中的 MVCC](#innodb-中的-mvcc)
  + [B-Tree索引](#b-tree索引)
  + [MyISAM与InnoDB存储引擎](#myisam与innodb存储引擎)
    * [MyISAM 索引](#myisam-索引)
    * [InnoDB 索引](#innodb-索引)
- [索引失效](#索引失效)
  + [数据库中的死锁](#数据库中的死锁)
- [红黑树](#红黑树)

<!-- /MarkdownTOC -->

# MySQL

# MySQL架构

分为四层：

1.连接层：客户端和连接服务

2.服务层：SQL分析和优化、缓存查询、跨存储引擎功能的实现

3.引擎层：MySQL数据的存储和读取

4.存储层：数据存储在硬盘

# SQL语句

1.建表
```
create table student(
  id int not null auto_increment,
  name varchar(20),
  score float(4,2),
  primary key (id)
 );
```
2.插入数据
```
insert into student(id, name, score) values
(1, 'Tom', 88.5),
(2, 'Jim', 40),
(3, 'David', 58),
(4, 'Judy', 74),
(5, 'Jack', 82.3);
```
|id|name|score|
|:-:|:-:|:-:|
| 1	| Tom | 88.5 |
| 2	| Jim | 40 |
| 3	| David | 58 |
| 4	| Judy | 74 |
| 5	| Jack | 82.3 |

|关键字|详解|
|:-:|-|
| float | float(7, 4)，其中 7 表示的是这个浮点数总的位数，4 表示小数点后面的位数，<br>就是说如果小数部分是 4 位，那么整数部分最多 3 位 |
| update | update student set name = 'Merry', score = 77.5 where id = 3; <br> update 用来更新表中的数据记录 |
| limit | select * from student limit m, n <br>其中 m 是指记录开始的 index，从 0 开始，表示第一条记录，n 是指从下标为 m 的记录开始，取 n 条。<br>例如：select * from tablename limit 2, 4  即取出第 3 条至第 6 条，共 4 条记录 <br> select * from student limit k; <br> 表示取查询结果中的前 k 条记录 |
| order by | select score from student order by score asc\|desc; <br> 对结果集按照一个列或者多个列进行排序，默认按照升序 |
| count() | select count(id) from student where score > 60; <br> count() 函数返回匹配指定列的值的个数，不包括 null |
| group by | select id from student group by score; <br> group by 语句用于结合聚合函数，根据一个或多个列对结果集进行分组 |
| inner join | select Websites.name, access_log.count, access_log.date from Websites <br> inner join access_log on Websites.id=access_log.site_id order by access_log.count; <br>只有两个表中条件完全匹配的行，才会返回<br>inner join 和 join 是一样的|
| left join | select Websites.name, access_log.count, access_log.date from Websites <br> left join access_log on Websites.id=access_log.site_id order by access_log.count desc; <br> Websites是左表，access_log是右表 <br> 左表对应的要查找的列全部返回，右表不匹配的显示为null |
| right join | select Websites.name, access_log.count, access_log.date from access_log <br> right join Websites on access_log.site_id=Websites.id order by access_log.count desc; <br> Websites是右表，access_log是左表 <br> 右表对应的要查找的列全部返回，左表不匹配的显示为null |
| full join | mysql不支持full join |
| having | SELECT Websites.name, Websites.url, SUM(access_log.count) AS nums FROM <br> (access_log INNER JOIN Websites ON access_log.site_id=Websites.id) <br> GROUP BY Websites.name HAVING SUM(access_log.count) > 200;<br>
在 SQL 中增加 HAVING 子句原因是，WHERE 关键字无法与聚合函数一起使用<br>
HAVING 子句可以让我们筛选分组后的各组数据 |



知乎上面的高分回答：如何学习SQL?(https://www.zhihu.com/question/19552975)

在线练习网站：http://sqlfiddle.com/ 

在线使用MySQL：https://sqltest.net/

3.机器怎么读取SQL语句？

1.FROM <br>
2.ON <br>
3.JOIN <br>
4.WHERE <br>
5.GROUP BY <br>
6.HAVING <br>
7.SELECT <br>
8.DISTINCT <br>
9.ORDER BY <br>
10.LIMIT <br>

# 事务

1.啥叫事务？
 
事务就是针对数据库中的数据记录的一组操作

2.事务有哪些特性？

ACID，4个特性

A，Atomic，原子性，就是指事务中的操作时一个整体，不可拆分

C，Consistency，一致性，就是指事务提交完成后，数据库中的数据要保持操作前后的一致性

I，Isolation，隔离性，不同的事务在并发操作的时候互不干扰

D，Durability，持久性，就是事务提交完毕，操作记录会被持久存储在数据库中

3.事务并发操作可能会带来哪些问题？

(1) 脏读，就是两个事务同时操作一条数据记录，其中一个读取了另外一个未提交的数据。简单说就是** A 事务读取了 B 事务未提交的数据**。

(2) 不可重复读，就是两个事务同时操作一条数据记录，其中一个事务提交了对这条记录的更改，另一个事务在提交前对这条记录做了两次查询，发现查询的结果不一样。简单说就是** A 事务读取了 B 事务提交的更改数据**。

(3) 幻读，就是两个事务同时对数据库中的表操作，事务一统计表中的数据记录，事务二向表中插入一条记录后提交，事务一两次统计的结果不一样。简单说就是 A 事务读取了 B 事务提交的新增数据

(4) 第一类丢失更新，就是两个事务同时操作一条数据记录，其中一个事务提交之后，另一个事务回滚了，导致之前提交的更改被覆盖。简单说就是事务 A 回滚操作覆盖了 事务 B 的提交的更新数据。

(5) 第二类丢失更新，就是两个事务同时操作一条数据记录，其中一个先提交，另一个后提交，后提交的覆盖了先提交的更改数据。简单说就是事务 A 的后提交覆盖了事务 B 先提交的更改数据。

4.数据库怎么实现并发事务的隔离？

通过对要操作的数据行或者数据表加锁，至于要加什么锁，是数据库根据用户设置的事务隔离级别，然后分析 SQL 语句根据需要自动加锁。

锁的类型主要有：

+ 行共享锁
+ 行独占锁
+ 表共享行独占锁
+ 表独占锁

5.事务的不同隔离级别能解决那些并发问题？

| 隔离级别 | 脏读 | 不可重复读 | 幻读 | 第一类丢失更新 | 第二类丢失更新 |
|:-|:-:|:-:|:-:|:-:|:-:|
| READ_UNCOMMITED | &radic; | &radic; | &radic; | &times; | &radic; |
| READ_COMMITED | &times; | &radic; | &radic; | &times; | &radic; |
| REPEATABLE_READ | &times; | &times; | &radic; | &times; |  &times; |
| SERIALIZABLE | &times; | &times; | &times; | &times; | &times; |

# 索引

1.索引是啥？

索引：是排好序的快速查找数据结构。索引会影响到查找和排序两个部分。

索引文件：用于记录这种联系 (关键码和它对应的数据记录的位置) 的文件组织结构

数据按照某种顺序存储在磁盘中后，我们称之为主文件。建立索引就是在主文件之上创建索引文件，**索引文件的格式为  (key, pointer)**， 其中key就是主文件中用来标识一条记录的key，pointer就是用来存储key所对应文件在磁盘中的存储位置。如下图所示：
<div align="center"> <img src="../pictures//index1.png"/> </div><br>
数据表中保存的是员工信息，选取 cid 作为索引文件的 key，pointer 就指向该 cid 对应的磁盘中的一条记录。假如要查询一条 cid==8 的记录，我们还没有建立索引，就需要先将磁盘中的数据读入内存中，然后查询这些数据中是否有 cid==8 的记录，如果有就取出来，如果没有就继续查询，直到找到该记录为止。查询过程中，将数据库中的记录读入内存时，我们将一条记录的所有信息全部读入进去，这样我们一次性读入内存中的数据条数就会减少，这样一次 IO 访问所能查询的数据条数就会减少，查询的效率就很低。如果已经建立索引，我们只需要将索引文件读入内存中，索引文件中只保存了 key 和 pointer ，所需要的存储空间就比较小，一次可以读入多个索引记录，这样一次 IO 访问，所能查询的数据条数就会增加，查询效率也就提高了。

2.建索引的目的

提高查询的效率，因为索引是有序的

3.什么时候建索引？

(1)主键自动创建唯一索引

(2)频繁作为查询条件的字段应该建立索引

(3)查询中与其他表关联的字段，外键关系建立索引

(4)高并发环境下建组合索引

(5)查询中排序的字段

(6)查询中统计或者分组的字段



4.什么时候不适合建索引

(1)表记录很少

(2)频繁更新的字段

(3)where 条件里用不到的字段

(4)某个字段的重复值较多的时候

5.索引怎么优化？

(1)需要使用关键字 EXPLAIN，例如：explain select * from user;

(2)EXPLAIN 能干嘛？

表的读取顺序

数据读取操作的操作类型

哪些索引可以使用

哪些索引被实际使用

表之间的引用

每张表有多少行被优化器查询

6.索引失效

(1)全值匹配我最爱

(2)最佳左前缀法则：复合索引 (name, age, pos) 只能单独用某个字段或者从 name 开始的连续的字段，比如 (name, age) 或者 (name, age, pos)

(3)不在索引列上做任何操作 (计算、函数、(自动或手动)类型转换)，会导致索引失效转向全表扫描

(4)存储引擎不能使用索引中范围条件右边的列: select * from table where name = "July" and age > 25 and pos ="manager"，这时 pos 列上的索引失效

(5)尽量使用覆盖索引 (只访问索引的查询(索引列和查询列一致))，减少 select *

(6)mysql 在使用不等于 (!=或者<>)的时候无法使用索引会导致全表扫描

(7)is null，is not null 也无法使用索引

(8)like 以通配符开头 ('%abc') mysql 索引失效会变成全表扫描：如果一定要用通配符，就使用覆盖索引去查询

(9)字符串不加单引号索引失效

(10)少用 or，用它来连接时索引失效：select * from table where name ='zh' or name = 'ch'，会导致索引失效

全值匹配我最爱，最左匹配要遵守

带头大哥不能死，中间兄弟不能断

索引列上少计算，范围之后全失效

like 百分写右边，覆盖索引不写星

不等空值还有 or，索引失效要少用

var 引号不可丢，索引高级也不难

参考博客：https://www.cnblogs.com/zjxiang/p/9160810.html

7.索引优化

慢查询的开启并捕获

explain + 慢 SQL 分析

show profile 分析 SQL 在 MySQL 服务器里面的执行细节和生命周期情况

SQL 数据库服务器的参数调优

## InnoDB 中的 MVCC

MVCC，多版本并发控制，采用了多版本并发控制来提高读操作的性能，MVCC 提供了非锁定读：不需要等待访问行上的锁释放，读取行的一个快照。只有 READ_COMMITTED 和 REPEATABLE_READ 两种事务隔离级别才能使用 MVCC。

InnoDB 中的 MVCC 是通过在每行记录的后面添加两列，分别是创建时间和删除时间，但是并不是记录实际的时间值，而是记录系统的版本号 (可以理解为操作数据的事务的 ID)，每一个事务在启动的时候，都有一个唯一的递增的版本号。

在执行数据库的查询的过程中，MVCC 操作的规则是：

查找事务创建时间小于等于当前事务ID，并且删除时间大于等于当前事务 ID 的数据行

假设现在有数据表 user：

|id|name|创建时间 (事务ID)|删除时间 (事务ID)|
|:-:|:-:|:-:|:-:|
| 1 | Tom | 1 | undefined |
| 2 | James | 1 | undefined |
| 3 | Rocket | 1 | undefined |

现在要执行下面两个事务：

事务 1 的 ID 为 2
```
start transaction;
select * from user;  //(1)
select * from user;  //(2)
commit; 
```

事务 2 的 ID 为 3

```
start transaction;
insert into user values(NULL,'Lucy');
commit;
```

假设在事务 1 执行到 (1) 位置，事务 2 开始执行并提交，那么数据表 user 中的记录将会变成：

|id|name|创建时间 (事务ID)|删除时间 (事务ID)|
|:-:|:-:|:-:|:-:|
| 1 | Tom | 1 | undefined |
| 2 | James | 1 | undefined |
| 3 | Rocket | 1 | undefined |
| 4 | Lucy | 3 | undefined |

当事务 1 继续执行到 (2) 位置，根据 MVCC 的操作规则，因为当前事务 1 的 ID 为 2，它只会查找 创建时间小于等于 2 的数据记录，那么新插入的数据行就不会被读取

以上内容参考博客：https://blog.csdn.net/whoamiyang/article/details/51901888

InnoDB 的加锁方式：

record lock：记录锁，也就是仅仅锁着单独的一行

gap lock：间隙锁，仅仅锁住一个区间 (注意这里的区间都是开区间，也就是不包括边界值)

next-key lock：record lock + gap lock，所以 next-key lock 也就半开半闭区间，且是下界开，上界闭，比如 (16, 21]。InnoDB 默认的加锁方式，主要是用来解决幻读的问题

InnoDB有三种行锁的算法：

如果在查询或者删除的过程中锁定了某些行或某一行，InnoDB 就会锁定一个区间

假设数据库中有一张数据表 store，id 是主键

|id|count|
|:-:|:-:|
| 2 | 11 |
| 3 | 12 |
| 8 | 13 |

执行如下操作：

(1) select * from store where id = 2 for update; InnoDB 会锁定 id = 2 的这一行

(2) delete from store where id = 10; 数据表中没有 id = 10 的记录，所以 InnoDB 会锁定 id 在 (2, 无穷大) 这个区间中的所有

(3) delete from store where id = 5; 数据表中没有 id = 5 的记录，但是 InnoDB 会锁定 id 在 (3, 5] 这个区间，因为在 id = 5 的前后都有数据记录，所以和上面的 id = 10 不同

(4) delete from store where id > 3; 锁定的区间就是 (3, 无穷大)

(5) delete from store where id < 3; 锁定的区间就是 (无穷小, 2]

注意：

(1)如果 SQL 语句中没有用到索引，那么就会锁定整张表，因为 InnoDB 中的锁是通过对索引加锁来实现的

(2)next-key lock 是为了防止幻读而设计的，只有在隔离级别是 REPEATABLE_READ 或者更高的隔离级别才有可能解决幻读的问题，READ_COMMITED 这种隔离级别下就不存在 next-key lock 这一说法

## B-Tree索引

在创建索引的过程中，如果主文件很大，那么一级索引相应的也会很大，查询的效率就会变得很低。这时候考虑建立二级甚至多级索引，假设一条索引记录为 16 字节，那么 1000 条索引记录，就需要 40 个块来存储。我们根据存储一级索引的块来建立二级索引，二级索引中一条记录指向一级索引中的一个块，二级索引一共需要 2 个块来存储。如下图所示：
<div align="center"> <img src="../pictures//index2.png"/> </div><br>
如果要查找一条数据记录，那么我们最多只需要查询存储二级索引的 2 个块，一级索引中的某一个块和存储数据库数据的某一个块就完成此次数据查询。

如果我们的数据量变得更大，索引的层数也会增多，这样将上图顺时针旋转 90 度就变成了 B-Tree 索引

B 树就是一个满足如下要求的 m 叉树：

1.如果一个节点不是叶子节点，也不是根节点，每个节点至少有 [m/2] 个孩子节点，至多有 m 个孩子节点

2.根节点至少有两个孩子

3.所有的叶子结点必须在同一层

4.B 树的创建过程是从下到上创建

B+ 树与 B 树的区别是，B+ 树的非叶子节点中不保存磁盘数据的 pointer 信息，所有的信息都保存在叶子节点中。B 树中的一个节点除了要保存孩子节点的 pointer 之外，还需要保存当前节点的 key 所对应的数据库中的某一条记录的 pointer

## MyISAM与InnoDB存储引擎

| MyISAM | InnoDB |
|-|-|
| B+ 树索引 | B+ 树索引 |
| 非聚集索引 | 聚集索引 |
| 索引文件和数据库文件分开存储 | 索引文件就是数据库文件 |
| 主码索引与辅码索引都是具有相同的结构 | 主码索引与辅码索引差别较大 |
| 主码和辅码索引的叶子节点中存储的都是存储该条数据记录的地址 | 主码索引叶子节点中存储的就是该条数据记录，辅码索引叶子节点中存储的是该条数据记录对于的主码|
| 不支持事务 | 支持事务 |
| 只缓存索引 | 缓存索引和真实数据 |
| 不支持主外键 | 支持主外键 |

### MyISAM 索引

MyISAM主码索引的原理图：
<div align="center"> <img src="../pictures//index3.png"/> </div><br>
&ensp;&ensp;&ensp;&ensp;图中数据表中的第一列是数据表的主键，B+树中的叶子节点中存储的是该主键对应的数据表中的一条记录的存储地址，根据这个存储地址就可以从磁盘中找到该记录，将其读入内存
<br></br>
MyISAM的辅码索引原理图：
<div align="center"> <img src="../pictures//index4.png"/> </div><br>
&ensp;&ensp;&ensp;&ensp;图中是对数据表的第二列建立索引，其索引结构与主码索引完全相同，叶子节点存储的是该辅码对于的数据表中某一条记录的存储地址，辅码索引中的key是可以重复的

### InnoDB 索引

InnoDB 的主码索引原理图：
<div align="center"> <img src="../pictures//index5.png"/> </div><br>
&ensp;&ensp;&ensp;&ensp;图中的数据表就是按照B+树的结构来存储的，所以数据表本身就是主码索引，索引的叶子节点中直接存储的是该主键对应的数据表中的一条记录，而不是这条记录的存储地址。
<br></br>
InnoDB 的辅码索引的原理图：
<div align="center"> <img src="../pictures//index6.png"/> </div><br>
&ensp;&ensp;&ensp;&ensp;图中索引的叶子节点中存储的既不是一条数据记录，也不是数据记录的地址，而是主码，也就是说 InnoDB 的辅码索引是建立在主码索引之上的，在数据查询过程中需要先根据辅码索引找到目标记录对应的主码，然后再根据主码查找到目标记录。所以，InnoDB 的主码索引效率非常高，辅码索引的效率比较低。


参考的博客：https://blog.csdn.net/qq_26768741/article/details/53164202

# 索引失效

参考博客：https://blog.csdn.net/wuseyukui/article/details/72312574

## 数据库中的死锁

1.多个事务不同顺序锁定资源时，会产生死锁

2.多个事务同时锁定同一个资源，产生死锁

InnoDB目前处理死锁的方法是，将持有最小行级排他锁的事务进行回滚（相对比较简单的死锁回滚方法） 

# 红黑树

红黑树就是一种二叉搜索树，但是还需要满足如下要求：
 
1.节点非黑即红
 
2.根节点是黑色
 
3.叶子节点 (为 null 或者 nil) 是黑色
 
4.任意子树从根节点到叶子节点的任意一条路径，所经过的黑色节点个数是相同 (为 null 和 nil 的叶子节点也计算在内)
 
5.最长路径 (root->最远的 nil) 的距离不能超过最短路径 (root->最近的 nil) 的两倍
 
<div align="center"> <img src="../pictures//red-black tree1.jpg"/> </div><br>
 
红黑树的优点：

红黑树并不追求“完全平衡”——它只要求部分地达到平衡要求，降低了对旋转的要求，从而提高了性能。

红黑树能够以 <a href="https://www.codecogs.com/eqnedit.php?latex=O(log_{2}^{n})" target="_blank"><img src="https://latex.codecogs.com/gif.latex?O(log_{2}^{n})" title="O(log_{2}^{n})" /></a> 的时间复杂度进行搜索、插入、删除操作。此外，由于它的设计，任何不平衡都会在三次旋转之内解决。当然，还有一些更好的，但实现起来更复杂的数据结构 能够做到一步旋转之内达到平衡，但红黑树能够给我们一个比较“便宜”的解决方案。红黑树的算法时间复杂度和 AVL 相同，但统计性能比 AVL 树 (平衡二叉树) 更高。


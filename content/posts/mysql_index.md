---
title: "Mysql_index"
date: 2022-12-15T14:47:28+08:00
draft: true
---

# 索引

## 1. 索引

在数据库中索引最核心的作用是：**加速查找**。  例如：在含有300w条数据的表中查询，无索引需要700秒，而利用索引可能仅需1秒。

```
mysql> select * from big where password="81f98021-6927-433a-8f0d-0f5ac274f96e";
+----+---------+---------------+--------------------------------------+------+
| id | name    | email         | password                             | age  |
+----+---------+---------------+--------------------------------------+------+
| 11 | wu-13-1 | w-13-1@qq.com | 81f98021-6927-433a-8f0d-0f5ac274f96e |    9 |
+----+---------+---------------+--------------------------------------+------+
1 row in set (0.70 sec)

mysql> select * from big where id=11;
+----+---------+---------------+--------------------------------------+------+
| id | name    | email         | password                             | age  |
+----+---------+---------------+--------------------------------------+------+
| 11 | wu-13-1 | w-13-1@qq.com | 81f98021-6927-433a-8f0d-0f5ac274f96e |    9 |
+----+---------+---------------+--------------------------------------+------+
1 row in set (0.00 sec)

mysql> select * from big where name="wu-13-1";
+----+---------+---------------+--------------------------------------+------+
| id | name    | email         | password                             | age  |
+----+---------+---------------+--------------------------------------+------+
| 11 | wu-13-1 | w-13-1@qq.com | 81f98021-6927-433a-8f0d-0f5ac274f96e |    9 |
+----+---------+---------------+--------------------------------------+------+
1 row in set (0.00 sec)
```

在开发过程中会为哪些 经常会被搜索的列 创建索引，以提高程序的响应速度。例如：查询手机号、邮箱、用户名等。





### 1.1 索引原理

为什么加上索引之后速度能有这么大的提升呢？ 因为索引的底层是基于B+Tree的数据结构存储的。

![image-20210526160040895](assets/image-20210526160040895.png

![image-20210526155746811](/Users/edy/GoProjects/zhougongjin555.github.io/content/posts/assets/image-20210526155746811.png)

![image-20210526160519425](/Users/edy/GoProjects/zhougongjin555.github.io/content/posts/assets/image-20210526160519425.png)

很明显，如果有了索引结构的查询效率比表中逐行查询的速度要快很多且数据量越大越明显。

B+Tree结构连接：https://www.cs.usfca.edu/~galles/visualization/BPlusTree.html



数据库的索引是基于上述B+Tree的数据结构实现，但在创建数据库表时，如果指定不同的引擎，底层使用的B+Tree结构的原理有些不同。

- myisam引擎，非聚簇索引（数据 和 索引结构 分开存储）

- innodb引擎，聚簇索引（数据 和 主键索引结构存储在一起）

  


#### 1.1.1 非聚簇索引（mysiam引擎）

```sql
create table 表名(
    id int not null auto_increment primary key, 
    name varchar(32) not null,
    age int
)engine=myisam default charset=utf8;
```


![image-20210526160040895](/Users/edy/GoProjects/zhougongjin555.github.io/content/posts/assets/image-20210526160040895.png)

![image-20210526155746811](/Users/edy/GoProjects/zhougongjin555.github.io/content/posts/assets/image-20210526155746811.png)

![image-20210526155118552](/Users/edy/GoProjects/zhougongjin555.github.io/content/posts/assets/image-20210526155118552.png)



#### 1.1.2 聚簇索引（innodb引擎）

```sql
create table 表名(
    id int not null auto_increment primary key, 
    name varchar(32) not null,
    age int
)engine=innodb default charset=utf8;
```


![image-20210526160040895](assets/image-20210526160040895.png

![image-20210526155746811](/Users/edy/GoProjects/zhougongjin555.github.io/content/posts/assets/image-20210526155746811.png)

![image-20210526160519425](/Users/edy/GoProjects/zhougongjin555.github.io/content/posts/assets/image-20210526160519425.png)

![image-20210526155250801](/Users/edy/GoProjects/zhougongjin555.github.io/content/posts/assets/image-20210526155250801.png)

在MySQL文件存储中的体现：

```
root@192 userdb # pwd
/usr/local/mysql/data/userdb
root@192 userdb # ls -l
total 1412928
-rw-r-----  1 _mysql  _mysql       8684 May 15 22:51 big.frm，表结构。
-rw-r-----  1 _mysql  _mysql  717225984 May 15 22:51 big.ibd，数据和索引结构。
-rw-r-----  1 _mysql  _mysql       8588 May 16 11:38 goods.frm
-rw-r-----  1 _mysql  _mysql      98304 May 16 11:39 goods.ibd
-rw-r-----  1 _mysql  _mysql       8586 May 26 10:57 t2.frm，表结构
-rw-r-----  1 _mysql  _mysql          0 May 26 10:57 t2.MYD，数据
-rw-r-----  1 _mysql  _mysql       1024 May 26 10:57 t2.MYI，索引结构
```





上述 聚簇索引 和 非聚簇索引 底层均利用了B+Tree结构结构，只不过内部数据存储有些不同罢了。

在企业开发中一般都会使用 innodb 引擎（内部支持事务、行级锁、外键等特点），在MySQL5.5版本之后默认引擎也是innodb。

- 区别

  ```
  innodb支持事务、行锁、外键，安全性较高，
  mysiam插入和查询更快以及删除相较都更快，比如查询会直接查找对应行号，而innodb需要一行一行遍历，删除，mysiam会直接重新建表，而mysiam会一行一行s
  ```

  

```sql
mysql> show create table users \G;
*************************** 1. row ***************************
       Table: users
Create Table: CREATE TABLE `users` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(32) DEFAULT NULL,
  `password` varchar(64) DEFAULT NULL,
  `ctime` datetime DEFAULT NULL,
  `age` int(11) DEFAULT '5',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=11 DEFAULT CHARSET=utf8
1 row in set (0.00 sec)

ERROR:
No query specified

mysql> show index from users \G;
*************************** 1. row ***************************
        Table: users
   Non_unique: 0
     Key_name: PRIMARY
 Seq_in_index: 1
  Column_name: id
    Collation: A
  Cardinality: 3
     Sub_part: NULL
       Packed: NULL
         Null:
   Index_type: BTREE   -- 虽然显示BTree，但底层数据结构基于B+Tree。
      Comment:
Index_comment:
1 row in set (0.00 sec)

ERROR:
No query specified

mysql>
```

innodb引擎，一般创建的索引：聚簇索引。



### 1.2 常见索引

在innodb引擎下，索引底层都是基于B+Tree数据结构存储（聚簇索引）。

在开发过程中常见的索引类型有：

- 主键索引：加速查找、不能为空、不能重复。 + 联合主键索引
- 唯一索引：加速查找、不能重复。  + 联合唯一索引  (可以有一个空值)
- 普通索引：加速查找。 + 联合索引



#### 1.2.1 主键和联合主键索引

- `primary key `: 创建主键索引

```sql
create table 表名(
    id int not null auto_increment primary key,   -- 主键
    name varchar(32) not null
);

create table 表名(
    id int not null auto_increment,
    name varchar(32) not null,
    primary key(id)
);

create table 表名(
    id int not null auto_increment,
    name varchar(32) not null,
    primary key(列1,列2)          -- 如果有多列，称为联合主键（不常用且myisam引擎支持）
);
```

```sql
alter table 表名 add primary key(列名);
```

```sql
alter table 表名 drop primary key;
```

注意：删除索引时可能会报错，**自增列必须定义为键 **。

```
ERROR 1075 (42000): Incorrect table definition; there can be only one auto column and it must be defined as a key

alter table 表 change id id int not null;
```

```sql
create table t7(
    id int not null,
    name varchar(32) not null,
    primary key(id)
);

alter table t6 drop primary key;
```



#### 1.2.2 唯一和联合唯一索引

- `unique`: 唯一索引

```sql
create table 表名(
    id int not null auto_increment primary key,
    name varchar(32) not null,
    email varchar(64) not null,
    unique ix_name (name),
    unique ix_email (email),
);

create table 表名(
    id int not null auto_increment,
    name varchar(32) not null,
    unique (列1,列2)               -- 如果有多列，称为联合唯一索引。
);
```

```sql
create unique index 索引名 on 表名(列名);
```

```sql
alter table 表名 add uniuqe index 索引名(字段1， 字段2， 字段3);
```

```sql
alter table 表名 drop unique index 索引名;
```



#### 1.2.3 索引和联合索引

- `index` : 普通索引

```sql
create table 表名(
    id int not null auto_increment primary key,
    name varchar(32) not null,
    email varchar(64) not null,
    index ix_email (email),
    index ix_name (name),
);

create table 表名(
    id int not null auto_increment primary key,
    name varchar(32) not null,
    email varchar(64) not null,
    index ix_email (name,email)     -- 如果有多列，称为联合索引。
);
```

```sql
create index 索引名 on 表名(列名);
```

```sql
drop index 索引名 on 表名;
```



### 1.3 操作表

在表中创建索引后，查询时一定要命中索引。



![image-20210526155746811](/Users/edy/GoProjects/zhougongjin555.github.io/content/posts/assets/image-20210526155746811.png)

在数据库的表中创建索引之后优缺点如下：

- 优点：查找速度快、约束（唯一、主键、联合唯一）
- 缺点：插入、删除、更新速度比较慢，因为每次操作都需要调整整个B+Tree的数据结构关系。

所以，在表中不要无节制的去创建索引啊。。。



在开发中，我们会对表中经常被搜索的列创建索引，从而提高程序的响应速度。

```sql
CREATE TABLE `big` (
    `id` int(11) NOT NULL AUTO_INCREMENT,
    `name` varchar(32) DEFAULT NULL,
    `email` varchar(64) DEFAULT NULL,
    `password` varchar(64) DEFAULT NULL,
    `age` int(11) DEFAULT NULL,
    PRIMARY KEY (`id`),                       -- 主键索引
    UNIQUE KEY `big_unique_email` (`email`),  -- 唯一索引
    index `ix_name_pwd` (`name`,`password`)     -- 联合索引
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```



一般情况下，我们针对只要通过索引列去搜搜都可以 `命中` 索引（通过索引结构加速查找）。

```sql
select * from big where id = 5;
select * from big where id > 5;
select * from big where email = "wupeiqi@live.com";
select * from big where name = "武沛齐";
select * from big where name = "kelly" and password="ffsijfs";
...
```



但是，还是会有一些特殊的情况，让我们无法命中索引（即使创建了索引），这也是需要大家在开发中要注意的。

- 类型不一致

  ```sql
  select * from big where name = 123;		-- 未命中
  select * from big where email = 123;	-- 未命中
  
  特殊的主键：
  	select * from big where id = "123";	-- 命中
  ```

- 使用不等于

  ```sql
  select * from big where name != "武沛齐";				-- 未命中
  select * from big where email != "wupeiqi@live.com";  -- 未命中
  
  特殊的主键：
  	select * from big where id != 123;	-- 命中
  ```

- or，当or条件中有未建立索引的列才失效。

  ```sql
  select * from big where id = 123 or password="xx";			-- 未命中
  select * from big where name = "wupeiqi" or password="xx";	-- 未命中
  特别的：
  	select * from big where id = 10 or password="xx" and name="xx"; -- 命中
  ```

- 排序，当根据索引排序时候，选择的映射如果不是索引，则不走索引。

  ```sql
  select * from big order by name asc;     -- 未命中
  select * from big order by name desc;    -- 未命中
  
  select name from big order by name desc; -- 命中
  特别的主键：
  	select * from big order by id desc;  -- 命中
  ```

- like，模糊匹配时。

  ```sql
  select * from big where name like "%u-12-19999";	-- 未命中
  select * from big where name like "_u-12-19999";	-- 未命中
  select * from big where name like "wu-%-10";		-- 未命中
  
  特别的：
  	select * from big where name like "wu-1111-%";	-- 命中
  	select * from big where name like "wuw-%";		-- 命中, 要求通配符必须在最后面才行
  ```

- 使用函数

  ```sql
  select * from big where reverse(name) = "wupeiqi";  -- 未命中
  
  特别的：
  	select * from big where name = reverse("wupeiqi");  -- 命中
  ```

- 最左前缀，如果是联合索引，要遵循最左前缀原则。

  ```sql
  如果联合索引为：(name,password)
      name and password       -- 命中
      name                 	-- 命中
      password                -- 未命中
      name or password       	-- 未命中
  ```

  

常见的无法命中索引的情况就是上述的示例。

对于大家来说会现在的最大的问题是，记不住，哪怎么办呢？接下来看执行计划。



### 1.4 执行计划

MySQL中提供了执行计划，让你能够预判SQL的执行（只能给到一定的参考，不一定完全能预判准确）。

```
explain + SQL语句;
```

![image-20210527074105599](/Users/edy/GoProjects/zhougongjin555.github.io/content/posts/assets/image-20210527074105599.png)

其中比较重要的是 type，他他SQL性能比较重要的标志，性能从低到高依次：`all < index < range < index_merge < ref_or_null < ref < eq_ref < system/const` 

- ALL，全表扫描，数据表从头到尾找一遍。(一般未命中索引，都是会执行权标扫描)

  ```sql
  select * from big;
  
  特别的：如果有limit，则找到之后就不在继续向下扫描.
  	select * from big limit 1;
  ```

- INDEX，全索引扫描，对索引从头到尾找一遍

  ```sql
  explain select id from big;
  explain select name from big;
  ```

- RANGE，对索引列进行范围查找

  ```sql
  explain select * from big where id > 10;
  explain select * from big where id in (11,22,33);
  explain select * from big where id between 10 and 20;
  explain select * from big where name > "wupeiqi" ;
  ```

- INDEX_MERGE，合并索引，使用多个单列索引搜索

  ```sql
  explain select * from big where id = 10 or name="武沛齐";
  ```

- REF，根据 索引 直接去查找（非键）。

  ```sql
  select *  from big where name = '武沛齐';
  ```

- EQ_REF，连表操作时常见。

  ```sql
  explain select big.name,users.id from big left join users on big.age = users.id;
  ```

- CONST，常量，表最多有一个匹配行,因为仅有一行,在这行的列值可被优化器剩余部分认为是常数,const表很快。

  ```sql
  explain select * from big where id=11;					-- 主键
  explain select * from big where email="w-11-0@qq.com";	-- 唯一索引
  ```

- SYSTEM，系统，表仅有一行(=系统表)。这是const联接类型的一个特例。

  ```sql
   explain select * from (select * from big where id=1 limit 1) as A;
  ```



其他列：

```
id，查询顺序标识

z，查询类型
    SIMPLE          简单查询
    PRIMARY         最外层查询
    SUBQUERY        映射为子查询
    DERIVED         子查询
    UNION           联合
    UNION RESULT    使用联合的结果
    ...
    
table，正在访问的表名

partitions，涉及的分区（MySQL支持将数据划分到不同的idb文件中，详单与数据的拆分）。 一个特别大的文件拆分成多个小文件（分区）。

possible_keys，查询涉及到的字段上若存在索引，则该索引将被列出，即：可能使用的索引。
key，显示MySQL在查询中实际使用的索引，若没有使用索引，显示为NULL。例如：有索引但未命中，则possible_keys显示、key则显示NULL。

key_len，表示索引字段的最大可能长度。(类型字节长度 + 变长2 + 可空1)，例如：key_len=195，类型varchar(64)，195=64*3+2+1

ref，连表时显示的关联信息。例如：A和B连表，显示连表的字段信息。

rows，估计读取的数据行数（只是预估值）
	explain select * from big where password ="025dfdeb-d803-425d-9834-445758885d1c";
	explain select * from big where password ="025dfdeb-d803-425d-9834-445758885d1c" limit 1;
filtered，返回结果的行占需要读到的行的百分比。
	explain select * from big where id=1;  -- 100，只读了一个1行，返回结果也是1行。
	explain select * from big where password="27d8ba90-edd0-4a2f-9aaf-99c9d607c3b3";  -- 10，读取了10行，返回了1行。
	注意：密码27d8ba90-edd0-4a2f-9aaf-99c9d607c3b3在第10行
	
extra，该列包含MySQL解决查询的详细信息。
    “Using index”
    此值表示mysql将使用覆盖索引，以避免访问表。不要把覆盖索引和index访问类型弄混了。
    “Using where”
    这意味着mysql服务器将在存储引擎检索行后再进行过滤，许多where条件里涉及索引中的列，当（并且如果）它读取索引时，就能被存储引擎检验，因此不是所有带where子句的查询都会显示“Using where”。有时“Using where”的出现就是一个暗示：查询可受益于不同的索引。
    “Using temporary”
    这意味着mysql在对查询结果排序时会使用一个临时表。
    “Using filesort”
    这意味着mysql会对结果使用一个外部索引排序，而不是按索引次序从表里读取行。mysql有两种文件排序算法，这两种排序方式都可以在内存或者磁盘上完成，explain不会告诉你mysql将使用哪一种文件排序，也不会告诉你排序会在内存里还是磁盘上完成。
    “Range checked for each record(index map: N)”
    这个意味着没有好用的索引，新的索引将在联接的每一行上重新估算，N是显示在possible_keys列中索引的位图，并且是冗余的。
```


# MySQL学习笔记

**root password：**lyxsql

要搞清楚数据库、数据表之间的关系。

[toc]

## windows下shell连接数据库

要先打开MySQL Notifer，启动数据库

然后打开MySQL shell，默人是js脚本，执行`\connect root@127.0.0.1`，之后输入密码，连接并以root身份登录。

## Linux下命令行登录

启动MySQL服务：

```
sudo service mysql start
```

root用户登录

```
mysql -u root
```





## 一些命令：

`show databases;` 查看有哪些数据库

`use <数据库名>;`没有书名号，连接数据库（使用这个数据库）

`show tables;`查看目前使用的数据库中有哪些数据表

`exit;`退出；

### show:

`show columns from <表名>;`查看这张表中的字段名、数据类型、是否允许null，键信息、默认值以及其他信息。

`show status;`用于显示广泛的服务器状态信息

`show create database`/ `show create table`显示创建特定数据库或表的sql语句

`show grants`显示授予用户的安全限制



### create:

`create database library;`创建一个名为library的数据库（library 可以换成其他名字）

(创建之后要use library才能对这个新建的数据库进行进一步操作)

创建表

```sql
create table <表的名字>
(
<列名1>  数据类型(长度)
<列名2>  数据类型(长度)
<列名2>  数据类型(长度)
)engine=<引擎名>;
```

如果仅想在表不存在时创建，在表名后给出if not exists

数据类型后面还可以跟上属性：如：not null, null, auto_increment

#### 主键：

在创建表的时候定义，`primary key(列名)` （create table括号中的最后一行）,也可以在创建表之后定义。顾名思义是这个行的唯一标识。

可以创建由多个列组成的主键`primary key(列名1, 列名2)`

#### auto_increment:

类似于execl中每添加一行就自动加1（自动编号）

每个表只允许一个auto_increment列

#### default:

默认值`<列名1> 数据类型(长度) default 1` 意思是列1的值在插入行时如果没有给定，就设为1。

#### 引擎：

- InnoDB
- MEMORY
- MyISAM

可以不写，则默认使用MyISAM。

注意：外键不能跨引擎。

#### 更改表的结构

- alter table

  添加列`alter table <表名> add <列名> 数据类型(长度);`

  删除列`alter table <表名> drop column <列名>;`

  尽量在开始设计表的时候就设计好，后期尽量不要改动表的结构。

- drop table

  `drop table <表名>;`删除表

- rename table

  `rename table <old name> to <new name>;`



#### 常用数据类型：

| 数据类型 | 大小(字节) | 用途             | 格式              |
| -------- | ---------- | ---------------- | ----------------- |
| INT      | 4          | 整数             |                   |
| FLOAT    | 4          | 单精度浮点数     |                   |
| DOUBLE   | 8          | 双精度浮点数     |                   |
| ENUM     | --         | 单选,比如性别    | ENUM('a','b','c') |
| SET      | --         | 多选             | SET('1','2','3')  |
| DATE     | 3          | 日期             | YYYY-MM-DD        |
| TIME     | 3          | 时间点或持续时间 | HH:MM:SS          |
| YEAR     | 1          | 年份值           | YYYY              |
| CHAR     | 0~255      | 定长字符串       |                   |
| VARCHAR  | 0~255      | 变长字符串       |                   |
| TEXT     | 0~65535    | 长文本数据       |                   |



### 检索数据

#### select：

`select <列名1>, <列名2>, <列名3> from <表名>; ` 查询这些列的内容

`select * from <表名>`查询这张表所有列的内容

#### distinct关键字：

仅返回不同的值：

```sql
select distinct vent_id from products;
```

#### limit子句：

limit x （检索最前面的x行，如果不足x行，返回所有）

limit x,y （检索第x行开始的y行，表的首行是第0行）

#### order 排序子句：

`order by <列名>`

可以有很多列名，排的越前，优先级越高。

- desc 降序
- asc 升序（默认）

```sql
order by prod_price desc,prod_name #按价格从大到小排列，如果相同按名称排列
```

#### where

指定搜索条件`where prod_name='fuses'`

**在同时使用where和order by时，order by 要位于where之后**

常用操作符：

| 操作符  |        说明        |
| :-----: | :----------------: |
|    =    |        等于        |
|   <>    |       不等于       |
|   !=    |       不等于       |
|    <    |        小于        |
|   <=    |      小于等于      |
|    >    |        大于        |
|   \>=   |      大于等于      |
| between | 在指定的两个值之间 |
| is null |   具有null值的列   |

注：`where prod_price between 5 and 10`

`where prod_price IS NULL`

##### 组合where子句：

- and 且

- or 或 

- in 

  指定条件范围`where vend_id in(1002,1003)`

  是恰好等于这个值，而不是一个范围

  `where prod_price in(10,50)`检索的是价格为10和50的，而不是10-50之间的。

- not 否定

优先级可以用括号来组合

##### like:

- %通配符

  匹配任意个字符`where prod_name like 'jet%'`

- _ 通配符

  匹配一个字符

##### 正则表达式：

关键字：regexp 

检索方法同其他地方的正则表达式用法。

- 匹配基本字符 `where prod_name regexp '.000'` 搜索prod_name中有000的
- or匹配 `where prod_name regexp '1000|2000'`
- 匹配几个字符之一 `where prod_name regexp '[123] ton'`[]有点像or的用法
- 匹配范围 `where prod_name regexp '[1-5] ton'`
- 匹配特殊字符 `where prod_name regexp '\\.'`需要加上\\\（双反斜杠）作为转义字符
- 匹配字符类
- 匹配多个实例
- 定位符

#### 计算字段

计算字段本来不存储在数据库中，是在执行select时创建的。

##### 拼接concat()

```sql
select concat(vend_name,' ', vend_country) from vendors;
```

concat拼接串

如果想给拼接好的串命名用as <新的名字>

```sql
select concat(vend_name,' ', vend_country)  as vend_title
from vendors;
```

其他在拼接中常用的函数

- Rtrim()去掉右边空格
- LTrim()去掉左边空格
- Trim()去掉两边的空格

##### 算术计算

```sql
SELECT prod_id,
quantity, item_price,
quantity*item_price as expanded_price
from orderitems
where order_num = 20005;
```

#### 数据处理函数

1. 

- 文本处理
- 日期和时间处理
- 数值处理

以上直接查表

#### 汇总数据

`select AVG(prod_price) as avg_price from products;`

- AVG() 返回某列的平均值，列名作为参数必须给出，忽略null值
- COUNT() 返回某列的行数，count(*)对表中行的数目进行计算，count(column)对特定列中非null的数目进行计算
- MAX() 返回某列的最大值
- MIN() 返回某列的最小值
- SUM() 返回某列值之和

##### 组合使用示例

```sql
SELECT COUNT(*) as num_items,
min(prod_price) as price_min,
max(prod_price) as price_mas,
sum(prod_price) as price_sum
from products;
```

#### 分组数据

把数据分成多个逻辑组，对每个逻辑组进行计算。

创建分组： group by

例子：

```sql
select vend_id, /*注意这里要加逗号*/
count(*) as num_prods
from products
group by vend_id;
```

意思就是在product表中，按照vend_id的不同，进行分组，计算每组的数量。

注意：group by要出现在where之后，order by之前。

`group by vend_id WITH ROLLUP`可以得到每个分组的汇总。

可以用having来过滤分组，having 的作用有点像where，就是having是对group进行筛选，而where是对行进行筛选。 其操作和where是一样的。

```sql
/*选出数量大于等于2的cust_id分组*/
select cust_id, count(*) as orders
from orders
group by cust_id
having count(*)>=2;
```

where在数据分组前进行过滤，having在分组后对组进行过滤。

##### 小结：select子句顺序

|   子句   |         说明         |     是否必须使用     |
| :------: | :------------------: | :------------------: |
|  select  | 要返回的列或者表达式 |          是          |
|   from   |    从哪张表中检索    | 仅在表中选取数据时用 |
|  where   |       行级过滤       |          否          |
| group by |       分组说明       |          否          |
|  having  |       组级过滤       |          否          |
| order by |     输出排序顺序     |          否          |
|  limit   |     要检索的行数     |          否          |



#### 子查询

多重查询，例子

```sql
select cust_id
from orders
where order_num in (
SELECT order_num
from orderitems
where prod_id = 'TNT2');
```

使用子查询时需要注意限制有歧义的列名，必要时可以用`<表名>.<列名>`这样的写法



#### 联结表

关系表达而设计就是要保证把信息分解成多个表，一类数据一个表，然后通过常用的值（关系relationship）互相关联。

**外键：**某个表中的一列，包含另一个表的主键值。

```sql
SELECT vend_name, prod_name, prod_price
from vendors, products
WHERE vendors.vend_id = products.vend_id
order by vend_name, prod_name;
```

where子句非常重要，保证了联结条件。如果没有联结条件的话返回的结果是笛卡尔积，检索出的行的数目是第一个表中的行数乘以第二个表中的行数。

(理解为同时检索两个表？)

其实也可以使用inner join（内部联结的方法来查询）

```sql
SELECT vend_name, prod_name, prod_price
from vendors inner join products
on vendors.vend_id = products.vend_id;
```

**首选使用inner join语法**

联结多个表时，可以在where子句限定联结条件时使用and。

```sql
SELECT cust_name, cust_contact 
from customers, orders, orderitems 
where customers.cust_id = orders.cust_id 
and orderitems.order_num = orders.order_num 
and prod_id = 'TNT2';
```

有时候子查询和内部联结是可以互相转换的。

#### 创建高级联结

使用表的别名，相当于给表起一个暂时的新名字，使用起来比较方便，但是不能像列的别名一样返回给客户机，只能在查询的时候使用。

```sql
SELECT cust_name, cust_contact
from customers as c, orders as o, orderitems as oi
where c.cust_id = o.cust_id
and oi.order_num = o.order_num
and prod_id = 'TNT2';
```

- 自联结
- 自然联结
- 外部联结



#### 组合查询

union操作符

例如，想要查询所有价格小于等于5的所有物品，并且还想包括供应商1001和1002生产的所有物品。

- 用where子句查询

```sql
SELECT vend_id,prod_id, prod_price 
from products 
where prod_price <= 5 
or vend_id in (1001,1002);
```

- 用union进行组合查询

```sql
SELECT vend_id, prod_id, prod_price
from products
where prod_price <= 5
union
SELECT vend_id, prod_id, prod_price
from products
where vend_id in (1001,1002);
```

两者的查询结果是一样的。

使用union all可以不取消相同的行，比如有一行的prod_price小于5 而且vend_id = 1001，那么使用union all就会出现两次这个行，但是使用union或者where or，那就会自动去掉重复的行。

如果要对union的结果进行排序，要在最后加上order by，整个查询只能有一个order by。



#### 全文本搜索

注意：MyISAM引擎支持全文本搜索，而InnoDB引擎不支持全文本搜索。

在创建表的时候可以指定fulltext，但是建议先导入数据然后再修改表创建fulltext数据。建立索引之后数据库会自动进行维护。

在建立索引之后，使用Match() 和Against()执行全文本搜索，其中Match指定被搜索的列，Against指定搜索表达式。

```sql
SELECT note_text 
from productnotes 
where match(note_text) AGAINST('rabbit');
```

##### 查询扩展：

比如说你想找到左右提到anvils的注释，然后再想找出与你的搜索可能有关的其他行，即使他们不包含关键词anvils。

```sql
SELECT note_text from productnotes 
where MATCH(note_text) AGAINST('anvils' With query expansion);
```

##### 布尔文本搜索：

- 要匹配的词
- 要排斥的词（如果包含这个词就不返回）
- 排列提示（指定某些词更重要，更重要的词等级更高）
- 表达式分组
- 其他

```sql
SELECT note_text
from productnotes
where match(note_text) AGAINST('heavy -rope*'  in boolean mode);
/*查询含有heavy 但是没有以rope开头的单词的行*/
```

布尔操作符

| 布尔操作符 |                             说明                             |
| :--------: | :----------------------------------------------------------: |
|     +      |                       包含，词必须存在                       |
|     -      |                      排除，词必须不出现                      |
|     >      |                      包含，且增加等级值                      |
|     <      |                      包含，且减小等级值                      |
|     ()     |                       词组组成子表达式                       |
|     ~      |                      取消一个值的排序值                      |
|     *      |                          词尾通配符                          |
|     ""     | 定义一个短语（与单个词的列表不一样，它匹配整个短语以便包含或排除这个短语） |





### 插入数据

#### insert：

向表中插入数据：

```sql
insert into <表的名字>(列名a，列名b,列名c) values(value1,value2,value3);
```

如果要插入多行数据:

```sql
insert into <表名>(列名1,列名2,...,列名n)
values(
	value1,
	value2,
	..
	valuen 
	/* 第一行value的值 */
),
(
	value1,
	value2,
	..
	valuen 
	/* 第2行value的值 */
);
```

#### update 和 delete：

使用时一定要记得加上where子句，限定条件，不然很容易不小心删掉或覆盖了所有的行。

- 要更新/删除的表
- 列名和新值
- 确定要更新行的过滤条件

```sql
UPDATE customers /*customers是表名*/
set cust_name = 'The Fudds',
cust_email = 'elmer@fudd.com'
where cust_id = 10005;
```

如果仅是想删除这行某个列的值，直接set为null，如果要把整行删除掉，再使用delete

```sql;
delete from customers
where cust_id = 10006;
```

如果想要快速删除整张表的所有行，建议使用truncate table。



## 其他使用技巧

#### 使用视图

例子：假如要查询customers，orders和orderitems表中的custname和custcontact的内容，其中要导出prod_id为TNT2的数据，那按照基本的做法，应该是

```sql
SELECT cust_name, cust_contact
from customers, orders, orderitems
where customers.cust_id = orders.cust_id
and orderitems.order_num = orders.order_num
and prod_id = 'TNT2';
```

其中前四行可以看作构建了新的表，最后一行在这个新表中进行查询。如果可以构建名为productcustomers的虚拟表，后面还要查询时就会方便很多。

创建视图：

```sql
create or replace view productcustomers as 
SELECT cust_name, cust_contact, prod_id 
from customers, orders, orderitems 
where customers.cust_id = orders.cust_id 
and orderitems.order_num = orders.order_num;
```

- 创建视图 create view <视图名字>
- 删除视图 drop view <视图名字>
- 更新视图 create or replace view <视图名字>

对视图的查询和计算等和table一样。

比如:

```sql
SELECT cust_name, cust_contact ,prod_id
from productcustomers
where prod_id = 'FB';
```

视图的常见应用：

- 重用SQL语句
- 简化复杂的SQL操作，在编写擦汗寻后，可以方便地宠用它而不必知道他的基本查询细节
- 使用表的组成部分而不是整个表
- 保护数据。可以给用户授予表的特定部分的访问权限而不是整个表的访问权限。
- 更改数据格式和表示。视图可以返回与底层表的表示和格式不同的数据。

**视图是可以使用更新的，insert，update，delete语句都是可以用的，更新视图原表也会被更新，所以如果不能确定被更新的基数据，则无法更新视图**

- 分组（group by和having）
- 联结
- 子查询
- 并
- 聚集函数（max，min，count等）
- distinct
- 导出计算列

所以一般不更新视图，仅用来做数据查询。



#### 使用存储过程

存储过程简单说就是为以后的使用而保存的一条或多条MySQL语句的集合，可以将其视为批文件。

MySQL称存储过程的执行为调用，有点像编程里面的函数。

**创建存储过程**

```sql
delimiter //
CREATE procedure productpricing()
begin
SELECT AVG(prod_price) as priceaverage
FROM products;
end //
delimiter ;
```

其中delimiter语句定义了命令行实用程序的语句分隔符。如果用的不是命令行实用程序，可以不管这个。

**调用存储过程**

```sql
call productpricing();
/*call <函数名>*/
```

**删除调用过程：**`drop procedure productpricing;`

也可以使用变量来进行数据临时存储

```sql
use demo;
drop procedure productpricing; /*先删除原来的*/

/*创建存储过程*/
delimiter //  
create procedure productpricing(
out pl decimal(8,2),
out ph decimal(8,2),
out p decimal(8,2)
)
begin
	select min(prod_price)
	into pl
	from products;
    select max(prod_price)
    into ph
    from products;
    select avg(prod_price)
    into p
    from products;
end //
delimiter ;

/*调用存储过程*/
call productpricing(@pricelow, @pricehigh,@priceaverage);
/*查看变量值*/
select @pricelow, @pricehigh, @priceaverage;
```

创建智能存储过程

- 使用if等语句
- 注释
- 输入输出参数
- 局部变量定义：declare语句

```sql
-- name: ordertotal
-- parameters: onumber = order number
-- taxable = 0 if not taxable, 1 if taxable
-- ototal  = order total variable

delimiter //
create procedure ordertotal(
	in onumber int,
	in taxable boolean,
	out ototal decimal(8,2) 
)
begin
	-- declare variable for total
    declare total decimal(8,2);
    -- declare tax percentage,默认为6%
    declare taxrate int default 6;
    
    -- get the order total
    select sum(item_price*quantity)
    from orderitems
    where order_num = onumber
    into total;
    
    -- is taxable?
    if taxable then
		-- yes, so add taxrate to the total
        select total + (total/100*taxrate) 
        into total;
	end if;
    
    select total into ototal;
end//

delimiter ;

call ordertotal(20005,0,@total);
select @total;
```

**检查存储过程**

```sql
--检查存储过程
show create procedure <name>; 
--获得包括何时创建，谁创建等详细信息的存储过程列表
show procedure status like '<过滤模式>'
```



#### 使用游标

有时，需要在检索出来的行中前进或后退一行或者多行，这就需要使用游标，游标是一个存储在MySQL服务器上的数据库查询，它不是一条select语句，而是检索出来的数据集，在存储了游标时候，应用程序可以根据需要滚动或浏览其中的数据。

- 创建游标
- 打开游标
- 使用游标
- 关闭游标

```sql
delimiter //
drop procedure processorders;

create procedure processorders()
begin
	declare done boolean default 0; 
	declare o int;    -- 检索当前行的order列
    declare t decimal(8,2); -- 存储每个订单的合计
    
    -- 定义游标
	declare ordernumbers cursor for
    select order_num from orders;
    
    -- 当repeat由于没有更多的行供循环而不能继续时，出现这个条件
    declare continue handler for sqlstate '02000' set done=1;
    
    -- 创建了一个新表 保存存储过程生成的结果
    create table if not exists ordertotals
    (order_num int, total decimal(8,2));
    
    open ordernumbers;
    repeat 
		fetch ordernumbers into o;
        call ordertotal(o,1,t); -- 调用之前就创建好的存储过程
        insert into ordertotals(order_num,total) 
        values(o,t);
	until done end repeat; --repeat 反复执行 直到done为真
    
    close ordernumbers;
    
end //
delimiter ;

-- 该存储过程不返回数据，但是能创建和填充另一个表
select * from ordertotals;
```



#### 使用触发器

希望语句在某些事件发生时被执行。

触发器是MySQL响应一下任意语句而自动执行的一条MySQL语句

- delete
- insert
- update

创建触发器时，要给4条信息

- 唯一的触发器名（每张表唯一，最好做到这个数据库中唯一）
- 触发器关联的表
- 触发器应该响应的活动（delete， insert，update）
- 触发器何时执行

触发器按每个表每个时间每次地定义，每个表每个时间每次只允许一个触发器，因此，每个表最多支持6个触发器（每条insert update delete之前和之后）单一触发器不能与多个事件或多个表关联

**只有表才支持触发器，视图和临时表都不支持**

创建并使用触发器的例子：

```sql
create trigger neworder after insert on orders
for each row select new.order_num into @asd;

insert into orders(order_date,cust_id)
values(now(),10001);

select @asd;
```



#### 管理事务处理

InnoDB引擎支持事务处理，而MySAM引擎不支持。 

事务处理用来管理必须成批执行的MySQL操作，以保证数据库不包含不完整的操作结果，保证一组操作不会中途停止，或者不完全执行。

- 事务 transaction 指一组SQL语句
- 回退 rollback 指撤销指定SQL语句的过程
- 提交 commit 指将未存储的SQL语句结果写入数据表
- 保留点 savepoint 指事务处理中设置的临时占位符，可以对它发布回退。

下面是一个删除ordertotals表中order_num为20005的行的一个例子，但是在事务结束之后又回退了这个操作。（set SQL_SAFE_UPDATES是因为在安全模式下无法对非主键条件进行更新，所以暂时解除了安全模式）；

```sql
use demo;
SET SQL_SAFE_UPDATES = 0;
select * from ordertotals;
start transaction;
	delete from ordertotals 
    where order_num = 20005;
    select * from ordertotals;
	rollback;

select * from ordertotals;
set SQL_SAFE_UPDATES = 1;
```

commit

```sql
start transaction;
select * from orderitems;
select * from orders;
delete from orderitems where order_num = 20010;
delete from orders where order_num = 20010;
commit;
```

如果两条delete语句中有一句失败，那么都不会执行commit

**当commit结束或者rollback结束之后，事务会自动关闭**

设置保留点：

在复杂的事务中，有时候不需要全部回退，而是回退到合适的位置，那可以设置保留点`savepoint delete1`，delete1为保留点的名字。然后比如要回退到这个保留点则`rollback to delete1`;

也可以将MySQL的自动提交设置为否，这样即使不在事务处理也不会提交`set autocommit=0;`直到autocommit被设置为1。autocommit标志是针对每个连接而不是服务器的。



#### 全球化和本地化

需要适应不同的字符集来适应不同的排序和检索数据算法。

- 字符集：字母和符号的集合
- 编码：为某个字符集成员的内部表示
- 校对：规定字符如何比较的指令集

查看所支持的字符集`show character set;`

查看所支持校对的完整列表 `show collation;`

确定所使用的字符集和校对

```sql
show variables like 'character%';
show variables like 'collation%';
```

在创建表的时候指定字符集和校对

```sql
create table mytable(
columnn1 int,
columnn2 varchar(10)
) default character set hebrew
collate hebrew_general_ci;
```

还可以对列单独指定字符集

```sql
columnn2 varchar(10) character set latin1 collate latin1_general_ci
```



#### 安全管理

创建用户

```sql
create user lyx identified by 'lyx';
```

引号里面是密码

删除用户 `drop user lyx;`

查看用户权限 `show grants for lyx;`

设置权限要给出的

- 要授予的权限
- 被授予访问权限的数据库或表
- 用户名

```sql
grant select on demo .* to lyx;
```

给用户lyx授予对demo数据库进行select(查看)操作的权限。

revoke删除权限。

设置用户口令（登陆密码）

```sql
set password for lyx = Password('lyx')
```



#### 数据库维护

备份 mysqldump，mysqlhotcopy

检查表键是否正确 `analyze table customers;`

针对许多问题对表进行检查 `check table orders, orderitems;`



#### 改善性能
### 常见关键字

create 创建	alter 修改	 drop 扔掉	truncate 截断

insert 新建	delete删除	update 修改	query 查询	grant 授权	

revoke 撤销授权	commit 提交	rollback 回滚	savepoint 保存还原点

### 基本语句

#### 解锁/加锁账户：

alter user **username** account **unlock**;

alter user **username** account **lock**;

#### 修改密码：

alter user **username** identified by **password**;

#### 创建账户：

create user **username** identified by **password**;

#### 给用户授权/撤销授权：

grant **dba** to **username**;

revoke **dba** from **username**;

#### 删除用户：

drop user **username**;

#### 切换用户：

conn  username/password;

#### 查看当前用户：

show user;

#### 查看表结构

desc 表名；

#### 修改创建的表名

alter table 原表名  rename to 想更改表名；

#### 建表之后增加字段

alter table 表名 add  字段名  字段类型；

#### 建表之后修改字段名

alter table 表名 rename column 字段名 to 想要更改表名；

#### 建表之后修改字段数据类型

alter table 表名 modify 字段名  想要的数据类型；

#### 建表之后删除字段

alter table 表名 drop column 想删除字段；

#### 删除表

drop table 表名 purge;

#### 插入数据

insert into 表名() values();

#### 修改数据

update student set name = 'xiaogang';

#### 删除数据

delete from student where name = 'xiaoxin';

#### 查询数据

select * from student;

### 基本符号

#### like:

%：代表任意位的任意字符

_:代表一位上任意字符

#### escape逃离符：

指定一个字符逃离，保证like之后的字符串中出现的字符看作是普通字符。

Ex:select * from student where name like ‘xiao，_%’  escape ',';

### 运算符：

#### 多行函数：聚组函数：组函数

count（）记数sum（）求和avg（）平均 max（）最大min（）最小

#### 单行函数：

ceil（）：返回大于等于x的最小整数

floor（）：返回小于等于x的最大整数

#### round（）：四舍五入

round（a1,a2）:保留指定位小数位的四舍五入

Ex:select round(3.1415926,3) from dual;  //返回3.142

#### trunc（）:直接截断

trunc（a1,a2）:保留制定位数的小数

#### sign（）：求符号位 正数1 负数-1 零返回0

#### abs（）：且绝对值

#### power（a,b）：求a的b次方

#### sqrt（）：求正平方根

### 转换函数

#### to_number（c）：将一个字符类型的数字变成数值类型

#### to_char（）：将数值类型转换为字符串

1. 将数字转换为字符串

   select * from student where '456.12' = to_char(salary);

2. 常用于在货币单位，格式化字符串

   select to_char(123123123123.00,'999,999,999,999,999.00') from dual;

3. 日期转换

   to_char（日期，‘yyyy-MM-dd hh24:mi:ss’）

   JAVA: yyyy-MM-dd HH:mi:ss

   Oracle:yyyy-mm-dd hh24:mi:ss:xff      			ff3  毫秒数保留三位

#### to_date（c1,c2）

c1:字符类型的日期

c2:格式

取出年

select to_char（to_date（‘2018-04-04’，‘yyyy-mm-dd’）,'yyyy'） from dual;

取出月   ‘yyyy’换成‘month’

取出日  ‘yyyy’ 换成‘dd’

日期函数：

日期可以加减（整数）运算  单位是：天  相差结果是多少天

两个日子没法相加

yyyy 年  year 年	mm   月  month 带"月"的月份	ddd  年中的天	dd   月中的天

d    周中的天		hh   12小时制	hh24 24小时制	mi   分钟		ss   秒	xff  毫秒

#### add_months（c1,c2） 在某个日子上增加多少个月

c1:日期

c2:整数值

select add_months(sysdate,-1) from dual;

#### months_between(c1,c2) 计算两个日期之间的月份

计算方式：c1- c2

#### last_day():计算给定日期的所在月份的最后一天

#### next_day(c1,c2)

c1:日期

c2:周中的某天

### 字符函数

#### lower():转换成小写

#### upper():转换成大写

#### initcap():首字母大写

#### length():求长度

#### substr(c1,c2,c3) 截取字符串

c1:被截取的字符串

c2:从哪个位置开始截取

c3:截取长度   默认截取到最后

#### instr(c1,c2,c3,c4) 索引字符串

c1:被查询的字符串

c2:希望找到的字符

c3:从哪个位置开始找  默认为1

c4:第几次出现

select instr('woshizhizhuxia','z',1,2)  from dual;

#### concat(c1,c2) 拼接字符串  也可以用 ||  拼接字符串

#### Ipad(c1,c2,c3) 左侧补全

#### rpad(c1,c2,c3)右侧补全

c1:希望补全的字符串

c2:补全到多少位

c3:以哪个字符来补全

#### trim(c1) 默认c1两侧去除空格

#### trim(c1 from c2) 把c2的两侧溢出指定的c1

#### ltrim(c1,c2) 左侧去除

#### rtrim(c1,c2)右侧去除

c1:被去除的字符串

c2:去除的字符串  默认是空格

#### replace(c1,c2,c3)  完全替换

c1:原字符串

c2:被替换的字符串

c3:替换的字符串

### 通用函数

#### nvl():空值处理

#### nvl(字段，替换显示的内容)

#### nvl2():空值处理二代

#### nvl2(字段，不是空显示什么，是空显示什么)

#### decode(c1,c2,c3,c4,c5,....Cx,Cx+1)

c1：原来拿来判断的值

从第二个参数开始，每两个参数看作是一组，拿每一组的第一个参数和c1进行比较

如果相同则返回该组的第二个参数

第一次判断：c2==c1?c3:

第二次判断：c4==c1?c5:

如果参数个数是奇数个，并且最终判断没有相同的数据，则返回空

如果参数个数是偶数个，并且最终判断没有相同的数据，则返回最后一个参数的值

#### 条件取值语句：

，（case email               	 --开始条件取值

when '3'                  	  	 --当条件成立

then salary+200             	 --则

when '7' then salary-200 	 

else salary+500           		--默认值

end）                      	        --结束

#### 排序  order by

排序字段：desc 代表降序  asc 代表升序

order by 后面可以加多个字段

order by 字段1 desc,字段2 asc

#### 分组  group by(前面不能用where 可以在后面用having)

根据某一个或多个列上的值，将该列上相同的值所在的记录划分为一个组，这样一张表就可以被划分为多个组，如

果以字段A分组，那么只能直接查询A。

**或者用组函数统计其他字段，不能直接查询其他字段。**

### 约束

#### 主键约束：primary key

1. 可在表中唯一确定数据的列，主键列应为非空且唯一。
2. 一张表只能有一个主键

#### 外键约束：foreign key  也可以写references

在子表中，若一个列上的值引用了母表中主键上的值，这个列便是外键。

#### 非空：not null

#### 唯一：unique

#### 检查：check

- 如何添加约束

  - 在创建表时直接添加约束

  - ```oracle
    alter table 表名 add constraint 约束名 约束；
    Ex:
    alter table test add constraint a primary key(id);

    除not null（有些特殊）
    alter table test modify 列名 constraint 约束名 not null；
    Ex:
    alter table test modify salary constraint a not null;
    ```

- 如何删除约束

  ```oracle
  alter table 表名 drop constraint 约束名;
  ```

### 多表查询

#### 内连接：inner join on   可简写为  join on

展示出两张表中有对应关系的数据，可以理解为取交集；

#### 外连接

**左外连接：left join on **

展示左表中的全部数据和右表中与左表有对应关系的数据；

Ex:select * from student s left join class c on s.sid = c.id;

**右外连接；right join on**

展示右表中的全部数据与左表中与右表有对应关系的数据；

Ex:select * from student s right join class c on s.sid = c.id;

#### 全连接：full join on

显示两张表中有对应关系的数据，无对应关系的也展示。

#### 交叉连接（笛卡尔积）：cross join

显示两张表中的数据一一交叉对应。

#### 什么时候用多表查询？什么时候用子表查询？

#### 如果查询数据在多个表内，要使用多表连接。

#### 如果不使用子表的数据，只需要使用子表来当条件，需使用子表查询。

#### 子查询中使用了In some any all 这几个关键字，效率低，可以使用多表关联的方式。

#### rownum

rownum是一个伪列，也就是说不是真是存在的，它是随着结果集生成的。

rownum都是从1开始的。

当我们输入：select  *  from  表  where rownum >5；不会匹配出任何数据，因为oracle只能确定第一个是哪个，

其他的它并不知道从那开始，认定为false；

**想要实现 rownum > n **

rownum 本身是不行的，但是可以先查出一个记过集合，既子查询。	

*** 实现rownum > n 时注意问题**

1. rownum 不能与order by 同一查询语句中出现。（但在子查询中可使用order by）
2. 不能使用    表名.rownum  
3. 若rownum在where之后，只能写rownum > 0 || rownum < 任意数 || rownum >= 1

Ex:

```oracle
select * from
	(
		select rownum rn,e.* from 
			(
				select * from student order by salary 
			)e
		where rownum < 9
		//这里已经将取出的结果集再次排序
	)
where rn > 5; 
```

**分页时确定当页从哪开始**

开始数：int startcount = （curpage -1 ) * pagesize + 1;

结束数：int endcount = curpage * pagesize; 

#### rowid

是映射每一行物理地址的唯一标识，通常用于删除完全重复的数据。

#### 联合关键字： union

union:

select  * from school where name like '%大'

union 

select * from school where name like '北%';

若第一条语句中包含第二句中查询的内容，则只展示第一条语句查询的内容。

union all

若第一条语句中包含第二句中查询的内容，都展示出来。

#### intersect 求交集

只展示两条语句查询的内容重复部分。

#### minus 从第一个查询结果中减去第二个结果中重复出现的

#### in(效率低，用exists代替)      not in

表示条件符合查询结果中某一个值就成立。

Ex:select * from school where id in (select sid  from stduent);

select * from school where id not in (select sid  from stduent);

#### some/any

用法与In相同，In用于无符号，some/any 用与有符号。

#### all 表示所有的值都大或都小（min,max代表第二条语句中的最大值最小值）

\>any  ==  >min

<any  ==  <max

\>all    ==  >max

<all   ==  <min 

### 序列 sequence

一个单独的数据对象，是一个能够生成有序的整数列值的对象。

一般oracle调用序列来实现主键自增。

在一个新的会话中，必须要调用下一个值才能获得当前的值。

#### 如何创建序列

create sequence 序列名；

create sequence seq_student

increment by 1/-1  	--增长，一次增1   正数+1  负数-1

start with 1     	  	--从1开始    升序默认就是+1  降序-1

minvalue 1         		--最小值     -10的26次幂

maxvalue 100       	--最大值     10的27次幂 序列超过最大值后会从minvalue 的数字重新开始

cycle              			--循环       默认nocycle

nocache;           		--不缓存     cache 4 默认生成20个序列号

#### 查询当前用户有多少序列

select * from user_sequences;

#### 获取下一个序列的值

序列名.nextval

#### 如何获取当前序列的值

select 序列名.currval  from dual;

#### 如何删除序列

drop sequence 序列名；

#### 如何修改序列

alter sequence 序列名  想要修改的序列条件；

#### 消除延迟段创建特性

alter session set deferred_segment_creation=false;

### View

#### 视图定义

视图	由SELECT查询语句定义的逻辑表，**只有定义并无数据**，在创建视图时，只是将视图的定义信息保存在[数据字典](https://blog.csdn.net/leshami/article/details/5711383)中，并不将任何实际数据复制即不在表空间中分配存储空间。可以像使用表一样使用视图（**使用视图的时候只是重新执行了SQL**）。

**通过视图看到的数据并不存储在视图中，数据存储在定义视图时查询的表（基表）。若基表中的数据发生改变视图也会自动更新。（若基表结构发生改变 ，则需要  ALTER VIEW  视图名 COMPILE）**

一个视图可以从另一个视图中产生。

**Oracle 的数据库对象分为五种：表，视图，序列，索引，同义词。**

#### 为什么要使用视图

- 使用视图，可以定制用户数据，焦距特定的数据（**即用户只能看到特定的行或列**）。

  Ex：不同职位需求的数据不同，可以通过视图将特定的数据展示给特定的需求。

- 简单

  Ex：在查询表中特定的某些数据时，需要调用一些函数，或者关联其他的表，最后写出的SQL语句长，复杂。若查询经常执行可以创建一个视图，再次查询时只SELECT * FROM 视图名  就OK了。

#### 视图基本语法

- 创建视图

  ```oracle
  CREATE [OR REPLACE][FORCE][NOFORCE] VIEW view_name 
  AS SELECT_STATEMENT
  [WITH CHECK OPTION[CONSTRAINT constraint_name]]
  [WITH READ ONLY]
  ```

  > REPLACE: 如果创建视图时，已经存在此视图，则重新创建此视图，相当于覆盖。
  >
  > FORCE:强制创建视图，不论视图依赖的基表是否存在或者是否有权限创建。
  >
  > NOFORCE:只有在基表存在且拥有创建权限才创建视图。
  >
  > WITH CHECK OPTION :指出在视图上进行修改要符合SELECT_STATEMENT锁指定的限定条件。
  >
  > WITH READ ONLY:**默认可以通过对基表进行增删改查操作，WITH READ ONLY只允许查看视图，现实开发中并不会通过视图对基表进行增删改操作。**

- 视图的定义原则

  - 视图的查询可以使用复杂的 SELECT 语法，包括 连接/分组 和 子查询。
  - 没有 WITH CHECK OPTION 和 READ ONLY，查询中不能使用 ORDER BY 子句。
  - OR REPLACE:不删除原视图便可更改其定义并重建，并重新赋予对象权限。

- 删除视图

  - 删除视图的定义并不会影响基表的数据。
  - 只有视图所有者或者拥有DROP VIEW权限的用户可以删除视图。
  - 视图删除后，基于视图创建的其他视图将无效。

  ```oracle
  DROP VIEW view_name;
  ```

- 视图分类

  - 简单视图
    - 只从单表获取数据。
    - 不包含函数和数据组。
    - 可以进行DML操作（不建议）。
  - 复杂视图
    - 从多个表里获取数据。
    - 包含函数和数据组。

#### 小结

视图适用于查询数据。

不要通过视图更改基表数据！

不要通过视图更改基表数据！

不要通过视图更改基表数据！

重要事情说三遍。

### Tablespace

#### 概念

表空间:是一个或多个数据文件的**逻辑**集合。

表空间逻辑存储对象: 

​					永久段: 如表，索引，存储过程，视图。

​					临时段:如临时表或排序段

​					回滚段（UNDO）:主要用于事物回滚，恢复，撤销数据

逻辑结构:

block -->extent -->segment-->tablespace-->database

创建表空间:

```oracle
CREATE TABLESPACE  tablespace_name nologging 
DATAFILE ''            --文件存在位置
SIZE 100M              --表空间初始大小
AUTOEXTEND ON TEXT     --下一次扩展大小
MAXSIZE                --最大值
```

在创建时给用户指定表空间

```oracle
CREATE USER user_name IDENTIFY BY password DEFAULT TABLESPACE tablespace_name;
```

更改用户表空间

```oralce
ALTER USER user_name DEFAULT TABLESPACE tablespace_name;
```

删除表空间（删除表空间后，需要为原指向此表空间的用户重新指定表空间）

```oracle
DROP TABLESPACE tablespace_name [INCLUDING ONTENTS AND DATAFILES];
```

### Index

#### 索引类型:

1. 普通索引

   ```oracle
   CREATE INDEX index_name on table_name(字段)
   ```


1. 唯一索引 unique

   ```oracle
   CREATE UNIQUE INDEX index_name on table_name(字段)
   ```

2. 位图索引 bitmap  //这种索引适合用在数据量比较大，基数比较小的列   Ex:男/女

   ```oracle
   CREATE BITMAP INDEX index_name on table_name(字段)
   ```

3. 函数索引

   ```oracle
   CREATE INDEX index_name on table_name(函数（列名）)
   ```

#### 操作索引

1. 重命名索引

   ```oracle
   ALTER INDEX index_name rename to new_name;
   ```

2. 查看索引

   ```oracle
   SELECT index_name,index_type,tablespace_name,uniqueness from all_indexes where table_name = 'tablename';
   ```

3. 删除索引

   ```oracle
   DROP INDEX index_name;
   ```

#### 合理使用索引

大大加快数据的检索速度。

### 存储过程 procedure

所谓存储过程就是在服务器端，能够被一个或多个应用程序调用的一段sql语句集。

#### 创建存储过程：

```oracle
CREATE OR REPLACE PROCEDURE
过程名（参数名 in 参数类型，参数名 in 参数类型，参数名 out 参数类型，参数名 out 参数类型）
AS 
变量名 变量类型：=值；
begin
sql语句集
end;

Ex:
create or replace procedure
pro_hi(key in number,value out varchar)
as
begin
if key=1
then value = '你好'
if key=2
then value = '再见'
end if;
end if;
end;
/
```

存储过程可以没有参数，如果没有参数则过程名之后不能出现括号。

存储过程可以有参数，可有多个参数传入或传出，也可只有传入或传出。

传入参数用in标明，传出参数用out表明。

存储过程并没有返回值，而是通过返回参数来返回数据的。

#### 调用存储过程：

```oracle
declare
变量 类型：=初始值;
begin
过程名（参数，变量）;
end;

Ex:
declare 
val varchar2(20):='';
begin
pro_hi(1,val);
dbms_output.put_line(val);
pro_hi(2,val);
dbms_output.put_line(val);
end;
```

### 触发器 trigger

触发器，当某个事件发生时自动的隐式运行。

for each row 行级触发器

#### 创建触发器

```oracle
create or replace trigger trigger_name
before/after insert/update/delete on table_name
for each row
begin
sql语句集;
end;

Ex:
--创建表
create table good(
id int primary key,
good_name varchar2(20)
);

--创建序列
create sequence seq_ids;

--创建触发器
create or replace trigger tri_insert_good
before insert on good
for each row
begin
select seq_ids.nextval
into:new.id
from dual;   --从序列中生成一个新的数值，赋值给当前插入行的ID列
end;

--增加数据，触发器
insert into good(good_name)values('苹果');

--创建日志表
create table test_log(
name varchar2(20),
time date
);

--创建触发器
create or replace trigger test_dept
before delete or insert or update on dept
declare var_tag varchar2(20);--声明一个变量，存储对dept表执行的操作类型
begin
if inserting then
var_tag :='insert';
elsif updating then
var_tag :='update';
elsif deleting then
var_tag :='delete';
end if;
insert into test_log values(var_tag,sysdate);--向日志表中插入对dept表的操作信息
end;
--分别执行语句对dept表进行操作
insert into dept values(66,'教育部','济南');
update dept set loc='易途' where deptno = 66;
delete from dept where deptno=66;

select * from dept;
select * from test_log;
```

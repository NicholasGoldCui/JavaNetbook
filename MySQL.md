## 第1章 模型
### 1.1 实体转换原则
+ 若实体间联系为1：1时，可以在两个实体类型转换成两个关系模式中**任意**一个关系模式中加入另一个关系模式的主关键字作为实体联系的属性。
+ 若实体间联系为1：n时，则在n端实体类型转换的关系模式中**加入**1端实体类型的关键字实体联系的属性。
+ 若实体间联系为n：m时，则要将实体间联系也**转换**成关系模式，其属性为**两端**实体类型的关键字。

## 第2章 数据库与数据表
### 2.1 数据库
#### 2.1.1 创建和查看数据库
1. 创建数据库
create database 数据库名 [default] character set 编码方式  | [default] collate 排序规则;
语法说明：
+ character set：指定数据库的字符集名称；
+ collate：指定数据库的排序规则名称。

2. 查看数据库
show databases;

若要查看指定的数据库
show create database 数据库名称;

#### 2.1.2 修改数据库
语法如下：
```mysql
alter database 数据库名
[default] character set 编码方式
| [default] collate 排序规则;
```
#### 2.1.3 删除数据库
语法格式如下：
```mysql
drop database 数据库名;
```
**注意**
删除数据库会删除该数据库中所有的表和所有数据，且不能恢复，因此在执行删除数据库操作时要慎重。

#### 2.1.4 mysql的存储引擎
存储引擎就是数据的存储技术。mysql的核心就是存储引擎。
1. 查看mysql支持的存储引擎
语法格式如下：
```mysql
show engines;
```
innodb为默认存储引擎，只有innodb支持事务处理、分布式处理和支持保存点。
查询默认的存储引擎，语法格式如下：
```mysql
show variables like 'storage_engine';
```

### 2.2.1 整数类型

### 2.3 创建和操作数据表
#### 2.3.1 创建和查看数据表
1. 查看数据表
数据库创建成功后，可以使用show tables 语句查看数据库中的表。
使用use语句选择当前数据库
```mysql
use 数据库名;
```
查看数据表
```mysql
show tables;
```
empty set表示空集。
3. 使用create table语句创建表
语法格式如下：
```mysql
create [temporary] table 表名
( 字段1,
  字段2，
  …………
);
```
语法说明：
+ temporary:使用该关键字表示创建的表为临时表。
+ 字段定义：
```mysql
字段名 类型 [not null | null] [default 默认值] [auto_increment] [unique key | primary key] [comment '字符串'] [外键定义]
```
**注意**
表的名称不能为SQL语言的关键字，如create、update、order等。使用有意义i的英文词汇，词汇中间以下划线分隔。只能使用英文字母，数字，下划线，并以英文字母开头，不能超过32个字符，须见名知意，建议使用名词而不是动词。

4. 查看表结构
语法格式如下：
```mysql
describe 表名;
```
**提示**
describe 可以编写成desc。
使用show create table可以查看表的详细定义，还可以查看表使用的默认的存储引擎和字符编码，其语法格式如下：
```mysql
show create table 表名;
```

#### 2.3.2 修改表
1. 修改表明
格式如下：
```mysql
alter table 原表名 rename [to] 新表名;
```
2. 修改字段
修改字段可以实现修改字段名、字段类型等操作。
在一张表中，字段名称是唯一的。修改语法格式如下：
```mysql
alter table 表名 change 原字段名 新字段名 新数据类型;
```
**注意**
修改字段名时，即使数据类型与原字段名的数据类型相同，也要重新写一遍。
若只需要修改字段的类型，使用SQL语句语法格式如下：
```mysql
alter table 表名 modify 字段名 新数据类型;
```
**提示**
modify和change都可以改变字段的数据类型，但change可以在改变字段类型的同时，改变字段名。如果要使用change修改字段数据类型，那么change后面必须跟两个同样的字段名。
3. 修改字段的排列位置
语法格式如下：
```mysql
alter table 表名 modify 字段名1 数据类型 first | after 字段名2;
```
其中，字段名1指待修改位置的字段名称，数据类型是字段名1 的数据类型，参数first表示将字段名1设置为表的第一个字段；after字段名2则表示将字段名1排列到字段名2之后。
4. 添加字段
语法如下：
```mysql
alter table 表名 add 字段名 数据类型 [完整性约束条件] [first | after 已存在的字段名];
```
5. 删除字段
语法格式如下：
```mysql
alter table 表名 drop 字段名;
```
6. 修改表的存储引擎
语法格式如下：
```mysql
alter table 表名 engine=存储引擎名;
```
#### 2.3.3 复制表
1. 复制表结构即数据到新表
```mysql
create table 新表名 select * from 源表名;
```
2. 只复制表结构到新表
```mysql
create table 新表名 select * from 源表名 where false;
```
只复制表结构到新表的语法同赋值结构及数据的语法相同，只是查询的条件恒为false。
MySQL5.0后，实现表结构的复制还可以使用关键字like，语法格式如下：
```mysql
create table 新表名 like 源表名;
```
3. 复制表的部分字段及数据到新表
```mysql
create table 新表名 as(select 字段1,字段2,…… from 源表名);
```
**注意**
当源表和新表属于不同的数据库时，需要源表名前面加上数据库名，格式为“数据库名.源表名”。
#### 2.3.4 删除表
删除表时，表的结构、数据、约束等被全部删除。语法格式如下：
```mysql
drop table 表名;
```
若想同时删除多张表，只需要在drop table语句中列出多个表名，表名之间用逗号隔开。

### 2.4 实现数据的完整性
#### 2.4.1 primary key 约束
1.语法格式如下
```mysql
字段名 数据类型 primary key
```
#### 2.4.2 not null约束
not null约束也称非空约束，强制字段的值不能为null，它不等同于0或空字符，不能和任何值作比较。语法格式如下：
```mysql
属性名 数据类型 not null
```
#### 2.4.3 default约束
default约束即默认值约束，用于指定字段的默认值。
语法格式如下：
```mysql
属性名 数据类型 default 默认值
```
#### 2.4.4 unique约束
unique又称唯一性约束，是指数据表中一列或一组列中只包含唯一值。格式如下：
```mysql
属性名 数据类型 unique
```
**提示**
primary key 约束拥有自定义的unique约束。unique约束允许字段值为空，若建立unique约束的字段值不允许为空时，还需同时设置not null约束。
#### 2.4.5 foreign key约束
又称外键约束，约束的实现不只在单表中进行而是在表中的数据与另一个表中数据之间进行。
1. 使用SQL语句创建外键约束
定义外键约束的语法格式如下：
```mysql
constraint 外键名 foreign key (外键字段名) references 主表名 (主键字段名)
```
**提示**
主从关系中，主表被从表引用的字段应该具有主键约束或唯一性约束。
建立外键约束的表，其存储引擎必须是innodb，且不能是临时表。
4. 外键约束的级联更新和删除
外键约束实现了表间的引用完整性，当主表中被引用列的值发生变化时，为了保证表间数据的一致性，从表中与该值相关的信息也应该更新，语法格式如下：
```mysql
constraint 外键名 foreign key (外键字段名) references 主表名(主键字段名)
[on update { cascade | set null | no action | restrict }]
[on delete { cascade | set null | no action | restrict }]
```
参数说明：
+ cascade: 指定在更新和删除操作表中记录时，如果该值被其他表引用，则级联更新或删除从表中相应的记录。
+ set null: 更新和删除操作表记录时，从表中相关记录对应的值设置为null。
+ no action: 不进行任何操作。
+ restrict：拒绝主表更新或修改外键的关联列。

5. 删除外键约束
多数情况下，约束的使用是为了数据库中各种关系更加严谨，但也限制了数据操作的灵活。删除外键约束的语法格式如下：
```mysql
alter table 表名 drop foreign key 外键名;
```
### 2.5 添加和修改系统数据
#### 2.5.1 插入数据
1. 使用insert语句插入单条数据
语法格式如下：
```mysql
insert into 表名[(字段列表)] values(值列表);
```
2. 使用replace语句插入单条数据
```mysql
replace into 表名 [(字段列表)] values(值列表);
```
**提示**
首先尝试将记录插入到数据表中，若检测到表中已经有该记录(通过主键或唯一约束判断)，则执行替换记录操作。
4. 使用insert语句插入多条数据
语法格式如下：
```mysql
insert into 表名[(字段列表)] values(值列表1)[,(值列表2),……(值列表n)];
```
5. 使用replace语句插入多条数据
```mysql
replace into user(uid,uname,usex,upwd) values
(值列表1),
(…………),
(值列表n);
6. 插入其他表的数据
insert语句可以将一个表中查询出来的数据插入到另一个表中。语法格式如下：
```mysql
insert into 目标数据表(字段列表1)
select 字段列表2 from 源数据表 where 条件表达式;
```
7. insert语句的其他语法格式
语法格式如下：
```mysql
insert into 表名
set 字段名1=值1 [,字段名2=值2,……]
```
#### 2.5.2 修改数据
update语句用于更新数据表中的数据。语法格式如下：
```mysql
update 表名 set 字段名1=值1,字段名2=值2,……,字段名n=取值n [where 条件表达式];
```
#### 2.5.3 删除数据
1.使用delete语句删除数据
```mysql
delete from 表名 [where 条件表达式];
```
去掉where则会删掉全部
2. 使用truncate语句删除数据
可以无条件删除表中的所有记录，语法格式如下：
```mysql
truncate [table] 表名;
```
## 第3章 SQL语句
### 3.1 查询单表数据
#### 3.1.1 select语句
语法格式如下：
```mysql
select [all | distinct ] * | 列名1[,列名2,……,列名n] from 表名 [where 条件表达式]
[group by 列名 [asc | desc] [having 条件表达式] ]
[order by 列名 [asc | desc],……]
[limit [offset] 记录数];
```
参数说明：
+ group by:指定的列进行分组；其中having为可选参数，用于对分组后的结果进行筛选。
+ order by：指定的列进行排序。默认升序。
+ limit：用于限制查询结果集的行数。offset为1时，表示查询结果从第2条记录开始，以此类推。

#### 3.1.2 查询列
1. 查询所有列
用关键字“`*`” 表示所有列。
3. 计算列值
```mysql
select gdname,gdsaleqty*gdprice from goods;
```
4. 为查询结果中的列指定列标题
用as
#### 3.1.3 选择行
语法格式如下：
```mysql
where 条件表达式
```
1. 使用比较运算符
| 运算符 | 含义 | 运算符 | 含义 |
| --- | --- | --- | --- |
| = | 等于 | <>、!= | 不等于 |
| > | 大于 | < | 小于 |
| >= | 大于等于 | <= | 小于等于 |

2. 使用逻辑运算符
and,or,not三种运算符。语法格式如下：
```mysql
where [not] 表达式1 逻辑运算符 表达式2
```
3. 使用between……and运算符
语法格式如下：
```mysql
where 表达式 [not] between 初始值 and 终止值
```
4. 使用in运算符
语法格式如下：
```mysql
where 表达式 [not] in (值1,值2,值3,……,值n)
```
5. 使用like运算符
运算符“=”可以判断两个字符串是否完全相同。模糊查询用like，语法格式如下：
```mysql
where 列名 [not] like '字符串' [escape '转义字符']
```
通配符
| 通配符 | 说明 | 示例 |
| --- | --- | --- |
| % | 任意字符串 | s%:表示查询以s开头的任意字符串，如small |
| _ | 任何单个字符 | `_`s:表示查询以s结尾且长度为2的字符串，如as |

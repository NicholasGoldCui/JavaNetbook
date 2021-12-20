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

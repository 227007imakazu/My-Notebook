#  一、MySQL基础

## 数据库概念

数据库（Database）是按照数据结构来组织、存储和管理数据的仓库。

每个数据库都有一个或多个不同的 API 用于创建，访问，管理，搜索和复制所保存的数据。

我们也可以将数据存储在文件中，但是在文件中读写数据速度相对较慢。

所以，现在我们使用关系型数据库管理系统（RDBMS）来存储和管理大数据量。所谓的关系型数据库，是建立在关系模型基础上的数据库，借助于集合代数等数学概念和方法来处理数据库中的数据。

RDBMS 即关系数据库管理系统(Relational Database Management System)的特点：

- 1.数据以表格的形式出现
- 2.每行为各种记录名称
- 3.每列为记录名称所对应的数据域
- 4.许多的行和列组成一张表单
- 5.若干的表单组成database

| 名称           | 全称                                                         | 简称                               |
| :------------- | :----------------------------------------------------------- | :--------------------------------- |
| 数据库         | 存储数据的仓库，数据有组织的进行存储                         | DataBase（DB）                     |
| 数据库管理系统 | 操纵和管理数据库的大型**系统**软件                           | DataBase Management System（DBMS） |
| SQL            | 操作关系型数据库的编程语言，定义了一套操作关系型数据库统一标准 | Structured Query Language（SQL）   |

## RDBMS 术语

- **数据库:** 数据库是一些关联表的集合。
- **数据表:** 表是数据的矩阵。在一个数据库中的表看起来像一个简单的电子表格。
- **列:** 一列(数据元素) 包含了相同类型的数据, 例如邮政编码的数据。
- **行：**一行（元组，或记录）是一组相关的数据，例如一条用户订阅的数据。
- **冗余**：存储两倍数据，冗余降低了性能，但提高了数据的安全性。
- **主键**：主键是唯一的。一个数据表中只能包含一个主键。你可以使用主键来查询数据。
- **外键：**外键用于关联两个表。
- **复合键**：复合键（组合键）将多个列作为一个索引键，一般用于复合索引。
- **索引：**使用索引可快速访问数据库表中的特定信息。索引是对数据库表中一列或多列的值进行排序的一种结构。类似于书籍的目录。
- **参照完整性:** 参照的完整性要求关系中不允许引用不存在的实体。与实体完整性是关系模型必须满足的完整性约束条件，目的是保证数据的一致性。

MySQL 为关系型数据库(Relational Database Management System), 这种所谓的"关系型"可以理解为"表格"的概念, 一个关系型数据库由一个或数个表格组成

## 数据库登录与连接

### 1.进入数据库(cmd下)  

`mysql -u root -p`  Password：123456

### 2.连接MySQL

```python
import pymysql

# 打开数据库连接
db = pymysql.connect(host='localhost',user='root',password='123456',database='itcast',port=3306)

# 使用cursor()方法获取操作游标
cursor = db.cursor()

# 编写sql语句
sql="""
    create table Student(
    sno int primary key,
    sname varchar(20),
    age int,
    score int
    ) 
"""
# 插入
sql_01='insert into t_student (id,name,age,score) values(%s,%s,%s,%s)'
# 查询
sql_02='select * from t_student'
# 执行sql语句
try:
    cursor.execute(sql)
    cursor.execute(sql_01,(6,'小飞',18,99))
    cursor.execute(sql_02)
    results = cursor.fetchall() # 获取查询结果
    for row in results: # 循环打印每个元组
        print(row)
    db.commit() # 对于插入，更新，删除操作要提交事务
    print('Success!')
except Exception as e:
    print(e)
    db.rollback()
    print("Failed")
finally: # 关闭数据库连接
    db.close()

```

## 四类操作

**每一条命令都要在末尾加分号（与命令行不同）**

**数据库操作语句不区分大小写**

### 1. DDL

#### 库操作

1.  查询所有数据库  
    `SHOW DATABASES;`
2.  查询当前数据库  
    `SELECT DATABASE();`
3.  创建  
    `CREATE DATABASE[IF NOT EXISTS]数据库名[DEFAULT CHARSET 字符集][COLLATE 排序规则];`  
    \[\] 中可以省略  
    **字符集推荐使用utf8mb4**
4.  删除  
    `DROP DATABASE [IF EXISTS] 数据库名;`
5.  使用  
    `USE 数据库名;`

#### 表操作

1.  查询当前数据库所有表  
    `SHOW TABLES;`
2.  查询表结构  
    `DESC 表名;`
3.  查询指定表的建表语句  
    `SHOW CREATE TABLE 表名;`
4.  创建表

```sql
CREATE TABLE 表名(
    字段一 字段一类型 [COMMENT 字段1注释],
    字段二 字段二类型 [COMMENT 字段2注释],
    字段三 字段三类型 [COMMENT 字段3注释]
) [COMMENT 表注释];
```

**注释可以用单引号引用中文**  
**一旦为表起了别名，就不能再使用表名来指定对应的字段了，此时只能够使用别名来指定字段。**  
字段类型(类型后加unsigned 表示无符号)

**数值类型**

| 类型        | 大小   | 有符号范围                                            | 无符号范围                                             | 描述                   |
| :---------- | :----- | :---------------------------------------------------- | :----------------------------------------------------- | :--------------------- |
| TINYINT     | 1bytes | (-128，127)                                           | (0，255)                                               | 小整数值               |
| SMALLINT    | 2bytes | (-32768，32767)                                       | (0，65535)                                             | 大整数值               |
| MEDIUMINT   | 3bytes | (-8388608，8388607)                                   | (0，16777215)                                          | 大整数值               |
| INT/INTEGER | 4bytes | (-2147483648，2147483647)                             | (0，4294967295)                                        | 大整数值               |
| BIGINT      | 8bytes | (-2^63^，2^63^-1)                                     | (0，2^64^-1)                                           | 极大整数值             |
| FLOAT       | 4bytes | (-3.402823466 E+38，3.402823466351 E+38)              | 0 和 (1.175494351 E-38，3.402823466 E+38)              | 单精度浮点数值         |
| DOUBLE      | 8bytes | (-1.7976931348623157 E+308，1.7976931348623157 E+308) | 0 和(2.2250738585072014E-308，1.7976931348623157E+308) | 双精度浮点数值         |
| DECIMAL     | NAN    | 依赖于M(精度)和D(标度)                                | 依赖于M(精度)和D(标度)的值                             | 小数值（精度确定点数） |

**字符串类型**

| 类型       | 大小               | 描述                         |
| :--------- | :----------------- | :--------------------------- |
| CHAR       | 0-255 bytes        | 定长字符串(需要指定长度)     |
| VARCHAR    | 0-65535 bytes      | 变长字符串(需要指定长度)     |
| TINYBLOB   | 0-255 bytes        | 不超过255个字符的二进制数据  |
| TINYTEXT   | 0-255 bytes        | 短文本字符串                 |
| BLOG       | 0-65535 bytes      | 二进制形式的长文本数据       |
| TEXT       | 0-65535 bytes      | 长文本数据                   |
| MEDIUMBLOB | 0-16777215 bytes   | 二进制形式的中等长度文本数据 |
| MEDIUMTEXT | 0-16777215 bytes   | 中等长度文本数据             |
| LONGBLOG   | 0-4294967295 bytes | 二进制形式的极大文本数据     |
| LONGTEXT   | 0-4294967295 bytes | 极大文本数据                 |

**日期时间类型**

| 类型      | 大小 | 范围                                      | 格式                | 描述             |
| :-------- | :--- | :---------------------------------------- | :------------------ | :--------------- |
| DATE      | 3    | 1000-01-01 至 9999-12-31                  | YYYY-MM-DD          | 日期值           |
| TIME      | 3    | -838:59:59 至 838:59:59                   | HH:MM:SS            | 时间值或持续时间 |
| YEAR      | 1    | 1901 至 2155                              | YYYY                | 年份值           |
| DATETIME  | 8    | 1000-01-01 00:00:00 至9999-12-31 23:59:59 | YYYY-MM-DD HH:MM:SS | 混合日期和时间值 |
| TIMESTAMP | 4    | 1970-01-01 00:00:01 至2038-01-19 03:14:07 | YYYY-MM-DD HH:MM:SS | 时间戳           |



5.  添加字段  
    `ALTER TABLE 表名 ADD 字段名 类型 (长度) [ COMMENT 注释 ] [ 约束 ];`
6.  修改字段

-   字段名与字段类型修改  
    `ALTER TABLE 表名 CHANGE 旧字段名 新字段名 类型 (长度) [ COMMENT 注释 ] [ 约束 ];`
-   数据类型修改  
    `ALTER TABLE 表名 MODIFY 字段名 新数据类型 (长度);`

7.  删除字段  
    `ALTER TABLE 表名 DROP 字段名;`
8.  修改表名  
    `ALTER TABLE 表名 RENAME TO 新表名;`
9.  删除表  
    `DROP TABLE [ IF EXISTS ] 表名;`  
    在删除表后又重建该表（清空数据）  
    `TRUNCATE TABLE 表名;`

### 2\. DML

-   给指定字段添加数据  
    `INSERT INTO 表名 (字段名1, 字段名2, ...) VALUES (值1, 值2, ...);`  
    **注意指定的字段必须已经被定义于该表**
-   给全部字段添加数据  
    `INSERT INTO 表名 VALUES (值1, 值2, ...);`
-   批量添加数据

```sql
INSERT INTO 表名 (字段名1, 字段名2, ...) VALUES (值1, 值2, ...), (值1, 值2, ...), (值1, 值2, ...) ;

INSERT INTO 表名 VALUES (值1, 值2, ...), (值1, 值2, ...), (值1, 值2, ...) ;
```

-   修改数据  
    `UPDATE 表名 SET 字段名1 = 值1 , 字段名2 = 值2 , .... [ WHERE 条件 ] ;`  
    案例：修改id为1的数据, 将name修改为小昭, gender修改为 女  
    `update employee set name = '小昭' , gender = '女' where id = 1;`
-   删除数据  
    `DELETE FROM 表名 [ WHERE 条件 ] ;`  
    注意事项:  
    • DELETE 语句的条件可以有，也可以没有，如果没有条件，则会删除整张表的所有数据。  
    • DELETE 语句不能删除某一个字段的值(可以使用UPDATE，将该字段值置为NULL即可)。  
    • 当进行删除全部数据操作时，datagrip会提示我们，询问是否确认删除，我们直接点击Execute即可

### 3\. DQL

#### 3.1.基础查询

-   查询多个字段`SELECT 字段1, 字段2, 字段3 ... FROM 表名 ;`
-   查询所有字段`SELECT * FROM 表名 ;`
-   字段设置别名`SELECT 字段1 [ AS 别名1 ] , 字段2 [ AS 别名2 ] ... FROM 表名;`(可以不加AS)
-   去除重复记录`SELECT DISTINCT 字段列表 FROM 表名;`

#### 3.2.条件查询

语法：`SELECT 字段列表 FROM 表名 WHERE 条件列表 ;`  
几个特殊条件

-   不等于： <>或!=
-   在某闭区间内 ：BETWEEN … AND …
-   在in之后的列表中的值，多选一：IN(…)
-   模糊匹配(\_匹配单个字符, %匹配任意个字符)：LIKE 占位符  
    示例：  
    `select *from emp where name like '___';`会找名字三个字的  
    `select *from emp where name like '__';`会找名字两个字的  
    查询身份证号最后一位为x的员工

```sql
select * from emp where idcard like '%X';
select * from emp where idcard like '_________________X';
```

-   is null 和is not null

#### 3.3,聚合函数

将一列数据进行运算（纵向）
语法：`SELECT 聚合函数(字段列表) FROM 表名 ;`
常见聚合函数

| 函数  | 功能     |
| ----- | -------- |
| count | 统计数量 |
| max   | 最大值   |
| min   | 最小值   |
| avg   | 平均值   |
| sum   | 求和     |

#### 3.4.分组查询

语法：`SELECT 字段列表 FROM 表名 [ WHERE 条件 ] GROUP BY分组字段名 [ HAVING 分组后过滤条件 ];`
示例：

- 根据性别分组计算男女数量
  `select gender, count(*) from emp group by gender ;`
- 根据性别分组，统计男女平均年龄
  `select gender, avg(age) from emp group by gender ;`
- 查询年龄小于45的员工 , 并根据工作地址分组 , 获取员工数量大于等于3的工作地址
  `select workaddress, count(*) address_count from emp where age < 45 group by workaddress having address_count >= 3;`
  **根据什么进行分组，就要select 什么字段**
  **address_count是别名，相当于函数返回值的引用**

#### 3.5.排序查询

语法：`SELECT 字段列表 FROM 表名 ORDER BY 字段1 排序方式1 , 字段2 排序方式2 ;`
**ASC升序，DESC降序**

#### 6.分页查询

语法：`SELECT 字段列表 FROM 表名 LIMIT 起始索引, 查询记录数 ;`
起始索引从0开始，起始索引 = （查询页码 - 1）* 每页显示记录数。
**如果第一页查询十条数据，那么第二页的查询就从10开始（第一页Index0-9）**

**各语法执行顺序**：from … where … group by …having … select … order by … limit …

### 4. DCL

#### 4.1.管理用户

- 查询用户`select * from mysql.user;`
- 创建用户`CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';`
- 修改用户密码`ALTER USER '用户名'@'主机名' IDENTIFIED WITH mysql_native_password BY '新密码' ;`
- 删除用户`DROP USER '用户名'@'主机名' ;`

**Host代表当前用户访问的主机, 如果为localhost, 仅代表只能够在当前本机访问，是不可以远程访问的。 User代表的是访问该数据库的用户名。在MySQL中需要通过Host和User来唯一标识一个用户**

示例`create user 'heima'@'%' identified by '123456';`
**使用’%'通配符表示允许来自任何主机的连接。**

#### 4.2. 权限控制

| 权限               | 说明               |
| :----------------- | :----------------- |
| ALL,ALL PRIVILEGES | 所有权限           |
| SELECT             | 查询数据           |
| INSERT             | 插入数据           |
| UPDATE             | 修改数据           |
| DELETE             | 删除数据           |
| ALTER              | 修改表             |
| DROP               | 删除数据库/表/视图 |
| CREATE             | 创建数据库/表      |

- 查询权限`SHOW GRANTS FOR '用户名'@'主机名';`

- 授予权限`GRANT 权限列表 ON 数据库名.表名 TO '用户名'@'主机名';`

- 撤销权限`REVOKE 权限列表 ON 数据库名.表名 FROM '用户名'@'主机名';`

  **授权时， 数据库名和表名可以使用 * 进行通配，代表所有**

## 函数

（**其实在业务中，很少会使用这些数据库函数，因为数据一般都会在外部处理好再放入数据库，其更多的是做一个数据存储读取作用**）

### 1.字符串函数

| 函数                     | 功能                                            |
| :----------------------- | :---------------------------------------------- |
| CONCAT(s1,s2,...sn)      | 拼接字符串                                      |
| LOWER(str)               | 字符串转小写                                    |
| UPPER(str)               | 字符串转大写                                    |
| LPAD(str,n,pad)          | 用字符串pad左填充str的左边n个字符长度           |
| RPAD(str,n,pad)          | 用字符串pad右填充str的右边n个字符长度           |
| TRIM(str)                | 去掉字符串头部和尾部的空格                      |
| SUBSTRING(str,start,len) | 返回从字符串str从start位置起的len个长度的字符串 |

案例：由于业务需求变更，企业员工的工号，统一为5位数，目前不足5位数的全部在前面补0。比如： 1号员工的工号应该为00001。
`update emp set workno = lpad(workno, 5, '0');`

### 2.数值函数

| 函数       | 功能                             |
| :--------- | :------------------------------- |
| CEIL(x)    | 向上取整                         |
| FLOOR(x)   | 向下取整                         |
| MOD(x,y)   | 返回模x/y                        |
| RAND()     | 返回0~1的随机数                  |
| ROUND(x,y) | 求参数x的四舍五入值，保留y位小鼠 |

案例：通过数据库的函数，生成一个六位数的随机验证码
`select lpad(round(rand()*1000000 , 0), 6, '0');`

### 3.日期函数

| 函数                             | 功能                                              |
| -------------------------------- | ------------------------------------------------- |
| CURDATE()                        | 返回当前日期                                      |
| CURTIME()                        | 返回当前时间                                      |
| NOW()                            | 返回当前日期时间                                  |
| YEAR(date)                       | 获取date的年份                                    |
| MONTH(date)                      | 获取date的月份                                    |
| DAY(date)                        | 获取date的日期                                    |
| DATE_ADD(date,INTERVAL,exprtype) | 返回一个日期/时间值加上一个时间间隔expt后的时间值 |
| DATEDIFF(date1,date2)            | 返回date1到date2之间的时间                        |

### 4.流程函数

| 函数                                                       | 功能                                                         |
| ---------------------------------------------------------- | ------------------------------------------------------------ |
| IF(value,t,f)                                              | 如果value为true，返回t，否则返回f                            |
| IFNULL(value1,value2)                                      | 如果value1不为空返回value1，否则返回value2                   |
| CASE WHEN [vall] THEN [res1] ... ELSE [default] END        | 如果vall为true，返回res1，...（选择语句），否则返回default默认值 |
| CASE [expr] WHEN [vall] THEN [res1] ... ELSE [default] END | 如果expr的值等于vall，返回res1，...否则返回default默认值     |

## 约束

### 1.约束演示

| 约束                   | 描述                                                     | 关键字      |
| :--------------------- | :------------------------------------------------------- | ----------- |
| 非空约束               | 该字段的数值不为null                                     | NUT NULL    |
| 唯一约束               | 该字段的数据唯一，不可重复                               | UNIQUE      |
| 主键约束（实体完整性） | 主键是一行数据的唯一标识，要求非空且唯一                 | PRIMARY KEY |
| 默认约束               | 保存数据时，未指定该字段的值则保持默认值                 | DEFAULT     |
| 检查约束               | 保证字段符合某条件                                       | CHECK       |
| 外键约束（参照完整性） | 用来让两张表的数据之间建立连接，保证数据的一致性和完整性 | FOREIGN KEY |

**注意：约束是作用于表中字段上的，可以在创建表/修改表的时候添加约束**

演示：

```sql
CREATE TABLE tb user (
    id int AUTO_INCREMENT PRIMARY KEY COMMENT 'ID唯一标识',
    name varchar(10) NOT NULL UNIQUE COMMENT '姓名',
    age int check (age > 0 && age <= 120) COMMENT '年龄',
    status char(1) default '1' COMMENT '状态',
    gender char(1) COMMENT '性别'
);
```

(**AUTO_INCREMENT表示主键自增，在后续主键优化部分会提到**)

### 2.外键约束

建立两张表间的数据连接
语法：

```sql
CREATE TABLE 表名(
字段名 数据类型,
...
[CONSTRAINT] [外键名称] FOREIGN KEY (外键字段名) REFERENCES 主表 (主表列名)
);

//添加外键
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段名) REFERENCES 主表 (主表列名) ;
```

案例：为emp表的dept_id字段添加外键约束,关联dept表的主键id。
`alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references dept(id);`
此时不能删除或更新父表记录，因为存在外键约束。

删除外键
`ALTER TABLE 表名 DROP FOREIGN KEY 外键名称;`

删除/更新行为

| 行为           | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| NO ACTION      | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新。 (与 RESTRICT 一致) 默认行为 |
| RESTRICT(受限) | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新。 (与 NO ACTION 一致) 默认行为 |
| CASCADE(级联)  | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有，则也删除/更新外键在子表中的记录 |
| SET NULL       | 当在父表中删除对应记录时，首先检查该记录是否有对应外键，如果有则设置子表中该外键值为null (**这就要求该外键允许取null**) |
| SET DEFAULT    | 父表有变更时，子表将外键列设置成一个默认的值 (Innodb不支持)  |

语法：
`ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段) REFERENCES 主表名 (主表字段名) ON UPDATE CASCADE ON DELETE CASCADE;`

## 多表查询

### 1.一对多

案例: 部门 与 员工的关系
关系: 一个部门对应多个员工，一个员工对应一个部门
实现: 在多的一方建立外键，指向一的一方的主键

### 2.多对多

案例: 学生 与 课程的关系
关系: 一个学生可以选修多门课程，一门课程也可以供多个学生选择
实现: 建立第三张中间表，中间表至少包含两个外键，分别关联两方主键

### 3.一对一

案例: 用户与用户详情的关系
关系: 一对一关系，多用于单表拆分，将一张表的基础字段放在一张表中，其他详情字段放在另一张表中，以提升操作效率
实现: 在任意一方加入外键，关联另外一方的主键，并且设置外键为唯一(UNIQUE)

### 4.查询

语法：`select * from 表1 , 表2 where 外键=主键;`
如果没有where过滤，得到的数据是表1与表2的**笛卡儿积**
示例：`select * from tb_user,tb_user_edu where tb_user.id=tb_user_edu.userid;`

### 5.连接分类

- 连接查询
  - 内连接：相当于查询A,B交集部分数据
  - 左外连接：查询左表所有数据，以及两张表交集部分数据（左表悬浮元组对应第二张表的列置为null）
  - 右外连接：查询右表所有数据，以及两张表交集部分数据（右表悬浮元组对应第二张表的列置为null）
  - 自连接：当前表与自身的连接查询，**自连接必须使用表别名**

- 子查询

##### A.内连接

1. 隐式内连接
   语法：`SELECT 字段列表 FROM 表1 , 表2 WHERE 条件 ... ;`
2. 显式内连接
   语法：`SELECT 字段列表 FROM 表1 [ INNER ] JOIN 表2 ON 连接条件 ... ;`
   示例：查询每一个员工的姓名以及相关联部门的名称
   `select emp.name , dept.name from emp , dept where emp.dept_id = dept.id ;`
   起别名简化编写
   `select e.name,d.name from emp e , dept d where e.dept_id = d.id;`

**使用内连接虽然方便，但是对于一些空数据会丢失，所以建议用外连接**

##### B.外连接

1. 左外连接
   语法：`SELECT 字段列表 FROM 表1 LEFT [ OUTER ] JOIN 表2 ON 条件 ... ;`
2. 右外连接
   语法：`SELECT 字段列表 FROM 表1 RIGHT [ OUTER ] JOIN 表2 ON 条件 ... ;`

案例：查询emp表的所有数据, 和对应的部门信息
由于需求中提到，要查询emp的所有数据，所以是不能内连接查询的，需要考虑使用外连接查询。
表结构: emp, dept
连接条件: emp.dept_id = [dept.id](http://dept.id)

```sql
select e.*, d.name from emp e left outer join dept d on e.dept_id = d.id;
//别名版
select e.*, d.name from emp e left join dept d on e.dept_id = d.id;
```

##### C.自连接

1. 自连接查询
   语法：`SELECT 字段列表 FROM 表A 别名A JOIN 表A 别名B ON 条件 ... ;`
   案例：查询所有员工 emp 及其领导的名字 emp , 如果员工没有领导, 也需要查询出来
   `select a.name '员工', b.name '领导' from emp a left join emp b on a.managerid =b.id;`
   **在自连接查询中，必须要为表起别名，要不然我们不清楚所指定的条件、返回的字段，到底是哪一张表的字段。**
2. 联合查询
   把多次查询的结果合并为新的查询结果集

```sql
SELECT 字段列表 FROM 表A ...
UNION [ ALL ]
//union all 会将全部的数据直接合并在一起，union 会对合并之后的数据去重。
SELECT 字段列表 FROM 表B ....;
```

案例：将薪资低于 5000 的员工 , 和 年龄大于 50 岁的员工全部查询出来.

```sql
select * from emp where salary < 5000
union
select * from emp where age > 50;
```

##### D.子查询(嵌套)

语法：
`SELECT * FROM t1 WHERE column1 = ( SELECT column1 FROM t2 );`

- 标量子查询（子查询结果为单个值）
  案例：查询 “销售部” 的所有员工信息
  完成这个需求时，我们可以将需求分解为两步：
  ①. 查询 “销售部” 部门ID
  `select id from dept where name = '销售部';`
  ②. 根据 “销售部” 部门ID, 查询员工信息
  `select * from emp where dept_id = (select id from dept where name = '销售部');`
- 列子查询(子查询结果为一列)

| 操作符 | 描述                                 |
| ------ | ------------------------------------ |
| IN     | 在指定范围内                         |
| NOT IN | 不在指定范围内                       |
| ANY    | 子查询返回列表中，有任意一个满足即可 |
| SOME   | 与ANY等同。使用SOME的地方都能用ANY   |
| ALL    | 子查询返回列表的所有值都必须满足     |

- 案例：查询比财务部所有人工资都高的员工信息

```
select * from emp where salary > all ( select salary from emp where dept_id =(select id from dept where name = '财务部') );
```

- 行子查询(子查询结果为一行)
  案例：查询与 “张无忌” 的薪资及直属领导相同的员工信息
  `select * from emp where (salary,managerid) = (select salary, managerid from emp where name = '张无忌');`
- 表子查询(子查询结果为多行多列)

## 事务

简介：事务是一组操作的集合，它是一个不可分割的工作单位，事务会把所有的操作作为一个整体一起向系统提交或撤销操作请求，即这些操作要么同时成功，要么同时失败。

### 1.控制事务一

1. 查看/设置事务提交方式

```sql
SELECT @@autocommit ;
SET @@autocommit = 0 ;
```

1. 提交事务
   `COMMIT;`
2. 回滚事务
   `ROLLBACK;`

### 2.控制事务二

1. 开启事务
   `START TRANSACTION 或 BEGIN ;`
2. 提交事务
   `COMMIT;`
3. 回滚事务
   `ROLLBACK;`

示例

```sql
-- 开启事务
start transaction
-- 1. 查询张三余额
select * from account where name = '张三';
-- 2. 张三的余额减少1000
update account set money = money - 1000 where name = '张三';
-- 3. 李四的余额增加1000
update account set money = money + 1000 where name = '李四';
-- 如果正常执行完毕, 则提交事务
commit;
-- 如果执行过程中报错, 则回滚事务
-- rollback;
```

### 3.事务四大特性（ACID）

- 原子性（Atomicity）：事务是不可分割的最小操作单元，要么全部成功，要么全部失败。
- 一致性（Consistency）：事务完成时，必须使所有的数据都保持一致状态。
- 隔离性（Isolation）：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立环境下运行。
- 持久性（Durability）：事务一旦提交或回滚，它对数据库中的数据的改变就是永久的。

### 4.并发事务问题

- 脏读：一个事务读到另外一个事务还没有提交的数据。
- 不可重复读：一个事务先后读取同一条记录，但两次读取的数据不同，称之为不可重复读。
- 幻读：一个事务按照条件查询数据时，没有对应的数据行，但是在插入数据时，又发现这行数据已经存在，好像出现了 “幻影”。

### 5.事务隔离级别

| 隔离级别                      | 脏读 | 不可重复读 | 幻读 |
| ----------------------------- | ---- | ---------- | ---- |
| Read uncommitted(读未提交)    | √    | √          | √    |
| Read committed(读提交)        | ×    | √          | √    |
| Repeatable Read(默认)(读重复) | ×    | ×          | √    |
| Serializable(可串行化)        | ×    | ×          | ×    |

**可串行化：保证了事务之间的并发执行不会导致任何并发问题，就好像这些事务是按照某种顺序依次执行一样。**

- 查看级别
  `SELECT @@TRANSACTION_ISOLATION;`
- 设置级别
  `SET [ SESSION | GLOBAL ] TRANSACTION ISOLATION LEVEL { READ UNCOMMITTED |READ COMMITTED | REPEATABLE READ | SERIALIZABLE }`
  示例

```sql
-- 设置事务隔离级别为REPEATABLE READ
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
```

**注意：事务隔离级别越高，数据越安全，但是性能越低(通过加锁限制并发执行)。**

# 二、MySQL进阶

## 1.存储引擎

存储引擎就是存储数据、建立索引、更新/查询数据等技术的实现方式 。存储引擎是基于表的，而不是基于库的，所以存储引擎也可被称为表类型。我们可以在创建表的时候，来指定选择的存储引擎，如果没有指定将自动选择默认的存储引擎。

- 建表时创建引擎

```sql
CREATE TABLE 表名(
字段1 字段1类型 [ COMMENT 字段1注释 ] ,
......
字段n 字段n类型 [COMMENT 字段n注释 ]
) ENGINE = INNODB [ COMMENT 表注释 ] ;
```

- 查询引擎
  `show engines;`

### **存储引擎特点**

#### 1.InnoDB

- DML操作遵循ACID模型，支持事务；
- 行级锁，提高并发访问性能；
- 支持外键FOREIGN KEY约束，保证数据的完整性和正确性；
- 逻辑存储结  
  - 表空间 : InnoDB存储引擎逻辑结构的最高层，ibd文件其实就是表空间文件，在表空间中可以包含多个Segment段
  - 段 : 表空间是由各个段组成的，常见的段有数据段、索引段、回滚段等。InnoDB中对于段的管理，都是引擎自身完成，不需要人为对其控制，一个段中包含多个区
  - 区 : 区是表空间的单元结构，每个区的大小为1M。 默认情况下，
    InnoDB存储引擎页大小为16K，即一个区中一共有64个连续的页。
  - 页 : 页是组成区的最小单元，页也是InnoDB 存储引擎磁盘管理的最小单元，每个页的大小默认为 16KB。为了保证页的连续性，InnoDB 存储擎每次从磁盘申请 4-5 个区。
  - 行 : InnoDB 存储引擎是面向行的，也就是说数据是按行进行存放的，在每一行中除了定义表时所指定的字段以外，还包含两个隐藏字段(后面会详细介绍)。

#### 2.MyISAM（早期）

- 不支持事务，不支持外键
- 支持表锁，不支持行锁
- 访问速度快

#### 3.Memory（临时表）

- 内存存放
- hash索引

| 特点         | InnoDB | MyISAM | Memory |
| ------------ | ------ | ------ | ------ |
| 存储限制     | 64TB   | 有     | 有     |
| 事务安全     | 支持   | -      | -      |
| 锁机制       | 行锁   | 表锁   | 表锁   |
| B+tree索引   | 支持   | 支持   | 支持   |
| Hash索引     | -      | -      | 支持   |
| 全文索引     | 支持   | 支持   | -      |
| 空间使用     | 高     | 低     | N/A    |
| 内存使用     | 高     | 低     | 中等   |
| 批量插入速度 | 低     | 高     | 高     |
| 支持外键     | 支持   | -      | -      |

​		

### **行锁和表锁**

1. 行锁（Row Lock）：
   - 锁定粒度：行级别
   - 锁定范围：仅锁定特定行的数据
   - 并发性影响：行锁允许多个事务同时访问同一张表的不同行，因此具有更好的并发性能。其他事务可以并发地读取或修改表中的其他行，而不会被行锁阻塞。
   - 使用场景：适用于高并发环境下对表中不同行进行频繁读写操作的场景，可以最大程度地提高并发性能。
2. 表锁（Table Lock）：
   - 锁定粒度：表级别
   - 锁定范围：锁定整张表的数据
   - 并发性影响：表锁会阻塞其他事务对同一张表的读写操作，因为只有一个事务可以获取到表锁。这会导致并发性能下降，因为其他事务必须等待锁释放才能继续操作。
   - 使用场景：适用于对整张表进行大规模数据操作或需要保证数据一致性的场景。表锁可以确保在操作期间没有其他事务对表进行修改，从而避免数据不一致的问题。

### **存储引擎的选择**

- InnoDB: 是Mysql的默认存储引擎，支持事务、外键。如果应用对事务的完整性有比较高的要求，在并发条件下要求数据的一致性，数据操作除了插入和查询之外，还包含很多的更新、删除操作，那么InnoDB存储引擎是比较合适的选择。
- MyISAM ： 如果应用是以读操作和插入操作为主，只有很少的更新和删除操作，并且对事务的完整性、并发性要求不是很高，那么选择这个存储引擎是非常合适的。
- MEMORY：将所有数据保存在内存中，访问速度快，通常用于临时表及缓存。MEMORY的缺陷就是对表的大小有限制，太大的表无法缓存在内存中，而且无法保障数据的安全性。

## 2.索引

在无索引时候查数据效率很低(遍历表)，如果引入索引（平衡二叉树等），会极大的优化搜索速度，降低IO成本
不同的存储引擎有不同的索引结构

| 索引结构              | 描述                                                         |
| --------------------- | ------------------------------------------------------------ |
| B+Tree                | 最常见的索引类型                                             |
| Hash                  | 底层数据结构由哈希表实现，只有精确匹配索引列才有效，不支持范围查询 |
| R-Tree（空间索引）    | MyISAM引擎的一个特殊索引类型，主要用于地理空间数据类型，通常使用较少 |
| Full-text（全文索引） | 是一种通过建立倒排索引，快速匹配文档的方式。类似于Lucene，Solr，ES |

### B-Tree

B-Tree，B树是一种多叉路衡查找树，相对于二叉树，B树每个节点可以有多个分支，即多叉。
以一颗最大度数（max-degree）为5(5阶)的b-tree为例，那这个B树每个节点最多存储4个key，5个指针：

![B-Tree](E:\桌面\MyNoteBook\MySql\B-Tree.png)

### B+Tree

标准B+Tree：

- 所有的数据都会出现在叶子节点。
- 叶子节点形成一个单向链表。
- 非叶子节点仅仅起到索引数据作用，具体的数据都是在叶子节点存放的。

MySQL索引数据结构对经典的B+Tree进行了优化。在原B+Tree的基础上，增加一个指向相邻叶子节点的链表指针，就形成了带有顺序指针的B+Tree，提高区间访问的性能，利于排序。

![B+Tree](E:\桌面\MyNoteBook\MySql\B+Tree.png)

### Hash

Hash算法实现的键值对映射，如果发生Hash冲突，用链表解决
特点：

- Hash索引只能用于对等比较(=，in)，不支持范围查询（between，>，< ，…）

- 无法利用索引完成排序操作

- 查询效率高，通常(不存在hash冲突的情况)只需要一次检索就可以了，效率通常要高于B+tree索引

  **InnoDB中具有自适应hash功能，Hash索引是InnoDB存储引擎根据B+Tree索引在指定条件下自动构建的。**

| 分类                      | 含义                                                       | 特点               |
| ------------------------- | ---------------------------------------------------------- | ------------------ |
| 聚集索引(Clustered Index) | 将数据存储与索引放到一块，索引结构的叶子节点保存了行数据   | 必须有，且仅有一个 |
| 二级索引(Secondary Index) | 将数据与索引分开存储，索引结构的叶子节点关联的是对应的主键 | 可以存在多个       |

- 聚集索引的叶子节点下挂的是这一行的数据 。
- 二级索引的叶子节点下挂的是该字段值对应的主键值。

回表查询： 先到二级索引中查找数据，找到主键值，然后再到聚集索引中根据主键值，获取数据的方式，称之为回表查询。

### 索引语法

1. 创建索引（默认B+Tree）
   `CREATE [ UNIQUE | FULLTEXT ] INDEX index_name ON table_name (index_col_name,... ) ;`
2. 查看索引
   `SHOW INDEX FROM table_name ;`
3. 删除索引
   `DROP INDEX index_name ON table_name ;`

### 性能分析

MySQL 客户端连接成功后，通过 show [session|global] status 命令可以提供服务器状态信息。通过如下指令，可以查看当前数据库的INSERT、UPDATE、DELETE、SELECT的访问频次

```sql
-- session 是查看当前会话 ;
-- global 是查询全局数据 ;
SHOW GLOBAL STATUS LIKE 'Com_______';
```

#### **慢查询日志**

日志中执行速度大于一定时间的语句会被记录，方便选择优化
操作步骤

1. 打开VMware虚拟机，并打开FinalShell连接主机
2. 在Linux下操作打开慢查询日志
3. 查看慢查询日志（命令行`tail -f localhost-slow.log`）

#### **profile详细**

`SET profiling = 1;` 开启profiling

```sql
-- 查看每一条SQL的耗时基本情况

show profiles;

-- 查看指定query_id的SQL语句各个阶段的耗时情况

show profile for query query_id;

-- 查看指定query_id的SQL语句CPU的使用情况

show profile cpu for query query_id;
```

#### **explain**

EXPLAIN 或者 DESC命令获取 MySQL 如何执行 SELECT 语句的信息，包括在 SELECT 语句执行过程中表如何连接和连接的顺序。
语法:`EXPLAIN SELECT 字段列表 FROM 表名 WHERE 条件 ;`

| 字段         | 含义                                                         |
| ------------ | ------------------------------------------------------------ |
| id           | select查询的序列号，表示查询中执行select子句或者是操作表的顺序(id相同，执行顺序从上到下;id不同，值越大，越先执行) |
| select_type  | 表示 SELECT 的类型，常见的取值有 SIMPLE (简单表，即不使用表连接或者子查询)、PRIMARY (主查询，即外层的查询)UNION (UNION 中的第二个或者后面的查询语句)SUBOUERY (SELECT/WHERE之后包含了子查询)等 |
| type         | 表示连接类型，性能由好到差的连接类型为NULI、system、 const、type、possible key、key、key len、rows、filtered、eq_ref、ref、range、index、all |
| possible_key | 显示可能应用在这张表上的索引，一个或多个                     |
| key          | 实际使用的索引，如果为NULL，则没有使用索引                   |
| key_len      | 表示索引中使用的字节数， 该值为索引字段最大可能长度，并非实际使用长度，在不损失精确性的前提下， 长度越短越好 |
| rows         | MySQL认为必须要执行查询的行数，在innodb引擎的表中，是一个估计值可能并不总是准确的 |
| filtered     | 表示返回结果的行数占需读取行数的百分比， filtered 的值越大越好 |

### 索引使用

#### 1. 最左前缀法则

如果索引了多列（联合索引），要遵守最左前缀法则。最左前缀法则指的是查询从索引的最左列开始，并且不跳过索引中的列。如果跳跃某一列，索引将会部分失效(后面的字段索引失效)。

#### 2. 范围查询

联合索引中，出现范围查询(>,<)，范围查询右侧的列索引失效
尽量使用（>=,<=）

#### 3. 索引失效

- 在索引列上进行运算
- 字符串不加引号
- 头部模糊查询（%在关键字之前）
- or条件前没有索引（后面即使有也会失效）
- 评估：MySQL在查询时，会评估使用索引的效率与走全表扫描的效率，如果走全表扫描更快，则放弃索引，走全表扫描。 因为索引是用来索引少量数据的，如果通过索引查询返回大批量的数据，则还不如走全表扫描来的快，此时索引就会失效。

#### 4. Sql提示

在SQL语句中加入一些人为的提示来达到优化操作的目的
1). use index ： 建议MySQL使用哪一个索引完成此次查询（仅仅是建议，mysql内部还会再次进行评估）。
`explain select * from tb_user use index(idx_user_pro) where profession = '软件工程';`
2). ignore index ： 忽略指定的索引。
`explain select * from tb_user ignore index(idx_user_pro) where profession = '软件工程';`
3). force index ： 强制使用索引。
`explain select * from tb_user force index(idx_user_pro) where profession = '软件工程';`

#### 5. 覆盖索引

覆盖索引是指索引包含了查询所需的所有列，无需再次回表查询数据页。当查询条件涉及到联合索引的最左列时，如果联合索引中包含了查询所需的所有列，那么查询可以直接从索引中获取所需的数据，而无需再次回表查询。

通过使用覆盖索引，可以避免了额外的磁盘I/O操作，提高了查询的性能。因为从索引中读取数据比从数据页中读取数据更加高效。

（**经过联合索引的列，只要是按从左往右的顺序，就可以直接得到所有联合的列数据**）

#### 6. 前缀索引

当字段类型为字符串（varchar，text，longtext等）时，有时候需要索引很长的字符串，这会让索引变得很大，查询时，浪费大量的磁盘IO， 影响查询效率。此时可以只将字符串的一部分前缀，建立索引，这样可以大大节约索引空间，从而提高索引效率。
语法：`create index idx_xxxx on table_name(column(n)) ;`
（为column数据建立长度为n的前缀索引）

#### 7.索引设计原则

1). 针对于数据量较大，且查询比较频繁的表建立索引。

2). 针对于常作为查询条件（where）、排序（order by）、分组（group by）操作的字段建立索引。

3). 尽量选择区分度高的列作为索引，尽量建立唯一索引，区分度越高，使用索引的效率越高。

4). 如果是字符串类型的字段，字段的长度较长，可以针对于字段的特点，建立前缀索引。

5). 尽量使用联合索引，减少单列索引，查询时，联合索引很多时候可以覆盖索引，节省存储空间，避免回表，提高查询效率。

6). 要控制索引的数量，索引并不是多多益善，索引越多，维护索引结构的代价也就越大，会影响增删改的效率。

7). 如果索引列不能存储NULL值，请在创建表时使用NOT NULL约束它。当优化器知道每列是否包含NULL值时，它可以更好地确定哪个索引最有效地用于查询。

## 3.Sql优化

### 1. 插入

1. 批量插入
   `Insert into tb_test values(1,'Tom'),(2,'Cat'),(3,'Jerry');`
2. 手动控制事务

```sql
start transaction;

insert into tb_test values(1,'Tom'),(2,'Cat'),(3,'Jerry');

insert into tb_test values(4,'Tom'),(5,'Cat'),(6,'Jerry');

insert into tb_test values(7,'Tom'),(8,'Cat'),(9,'Jerry');

commit;
```

3. 主键顺序插入

4. 大批量数据使用load指令
   将数据脚本文件中的数据加载到表结构

```sql
-- 客户端连接服务端时，加上参数 -–local-infile
mysql –-local-infile -u root -p
-- 设置全局参数local_infile为1，开启从本地加载文件导入数据的开关
set global local_infile = 1;
-- 执行load指令将准备好的数据，加载到表结构中
load data local infile '/root/sql1.log' into table tb_user fields
terminated by ',' lines terminated by '\n' ;
```

```SQL
-- 当文件位于E盘路径下，可以使用以下示例来加载文件：

LOAD DATA LOCAL INFILE 'E/load_user_100w_sort.sql'
INTO TABLE tb_user
FIELDS TERMINATED BY ','
LINES TERMINATED BY '\n'
```

### 2. 主键优化

- 页与页通过指针连接
- 页分裂：主键乱序插入时，引擎内部会根据顺序，迁移一部分数据到新页后再在合适的位置插入该键，然后再重新调整链表指针（耗费性能）
- 页合并：当删除一些数据后，引擎会根据设定的阈值（通常为50%），尝试合并两页内容以优化空间使用
- 索引设计原则：
  满足业务需求的情况下，尽量降低主键的长度。
  插入数据时，尽量选择顺序插入，选择使用AUTO_INCREMENT自增主键。
  尽量不要使用UUID做主键或者是其他自然主键，如身份证号。
  业务操作时，避免对主键的修改。

### 3. order by 优化

MySQL两种排序
\1. using filesort:通过表索引或者全表扫描数据后在缓冲区完成排序
\2. using index:通过有序索引顺序扫描，无需再排序（性能好）
\3. 创建索引时如果未指定排序规则，则默认升序，此时如果执行降序排序，也会出现using filesort的现象，为了解决这个问题，可以在创建索引的时候规定好需要排序的列的排序规则`create index idx_user_age_phone_ad on tb_user(age asc ,phone desc);`
\4. 优化原则：
A. 根据排序字段建立合适的索引，多字段排序时，也遵循最左前缀法则。
B. 尽量使用覆盖索引。
C. 多字段排序, 一个升序一个降序，此时需要注意联合索引在创建时的规则（ASC/DESC）。
D. 如果不可避免的出现filesort，大数据量排序时，可以适当增大排序缓冲区大小sort_buffer_size(默认256k)。

### 4. group by 优化

group by 也可以通过索引来提高效率，并且同样遵循最左前缀法则（即没有按从左到右的顺序使用group by查询，则会使索引失效）

### 5. limit优化

当在进行分页查询时，如果执行 limit 2000000,10 ，此时需要MySQL排序前2000010 记录，仅仅返回 2000000 - 2000010 的记录，其他记录丢弃，查询排序的代价非常大。

优化思路: 一般分页查询时，通过创建 覆盖索引 能够比较好地提高性能，可以通过覆盖索引加子查询形式进行优化。
`explain select * from tb_sku t , (select id from tb_sku order by id limit 2000000,10) a where t.id = a.id;`
这条句子在子查询中获取符合条件的id列（用这种方式来分页），并且通过子表与原表的连接定位id的位置，并事先创建好id与预查询数据的**覆盖索引**，通过id快速查表

### 6. count优化

count聚合函数四种情况的效率分析

| COUNT用法   | 含义                                                         |
| ----------- | ------------------------------------------------------------ |
| COUNT(主键) | InnoDB 引擎会遍历整张表，把每一行的 主键id 值都取出来，返回给服务层服务层拿到主键后，直接按行进行累加(主键不可能为nu11) |
| COUNT(字段) | 没有not null 约束 : InnoDB 引擎会遍历整张表把每一行的字段值都取出来，返回给服务层，服务层判断是否为nu11，不为nul1，计数累加。    有not null 约束: InnoDB 引擎会遍历整张表把每一行的字段值都取出来，返回给服务层，直接按行进行累加。 |
| COUNT(数字) | InnoDB 引擎遍历整张表，但不取值。服务层对于返回的每一行，放一个数字“1”进去，直接按行进行累加。 |
| COUNT(*)    | InnoDB引擎并不会把全部字段取出来，而是专门做了优化，不取值，服务层直接按行进行累加。 |

按照效率排序的话，count(字段) < count(主键 id) < count(1) ≈ count(*)，所以尽量使用 count(*)。

### 7. update优化

InnoDB的行锁是针对索引加的锁，不是针对记录加的锁 ,并且该索引不能失效，否则会从行锁升级为表锁 。

所以在update时尽量根据索引（主键）进行更新，若是根据其他没有索引的字段进行更新，会降低多事务并行效率

## 4.视图/存储过程/触发器

### 1. 视图

视图（View）是一种虚拟存在的表。视图中的数据并不在数据库中实际存在，行和列数据来自定义视图的查询中使用的表，并且是在使用视图时**动态**生成的。

(**可以理解为c++的define预处理操作，只是记录语句逻辑，并不先计算，也可以看成一种长句子的“缩写”**)

通俗的讲，视图只保存了查询的SQL逻辑，不保存查询结果。

**但视图也可以看作一张存在着的虚拟表，可以和普通表一样进行各类包括多表连接等操作**

#### 1.1. 语法

- 创建
  `CREATE [OR REPLACE] VIEW 视图名称[(列名列表)] AS SELECT语句 [ WITH [ CASCADED | LOCAL ] CHECK OPTION ]`
- 查询

```sql
查看创建视图语句：SHOW CREATE VIEW 视图名称;

查看视图数据：SELECT * FROM 视图名称 ...... ;
```

- 修改

```sql
方式一：CREATE [OR REPLACE] VIEW 视图名称[(列名列表)] AS SELECT语句 [ WITH

[ CASCADED | LOCAL ] CHECK OPTION ]

方式二：ALTER VIEW 视图名称[(列名列表)] AS SELECT语句 [ WITH [ CASCADED |

LOCAL ] CHECK OPTION ]
```

- 删除
  `DROP VIEW [IF EXISTS] 视图名称 [,视图名称] ...`

在视图中插入行，如果主键不冲突，则可以插入基表中

#### 1.2. 检查选项

当使用WITH CHECK OPTION子句创建视图时，MySQL会通过视图检查正在更改的每个行，例如 插入，更新，删除，以使其符合视图的定义。 MySQL允许基于另一个视图创建视图，它还会检查依赖视图中的规则以保持一致性。为了确定检查的范围，mysql提供了两个选项： CASCADED 和 LOCAL，默认值为CASCADED 。

1. CASCADED 级联
   如果v2视图基于v1，那么检查v2时还会检查关联视图v1
2. LOCAL 本地
   如果v2视图基于v1，那么只会检查v2

#### 1.3. 视图更新

要使视图可更新，视图中的行与基础表中的行之间必须存在一对一的关系。如果视图包含以下任何一项，则该视图不可更新：

A. 聚合函数或窗口函数（SUM()、 MIN()、 MAX()、 COUNT()等）

B. DISTINCT

C. GROUP BY

D. HAVING

E. UNION 或者 UNION ALL

#### 1.4. 视图作用

1. 简单：简化用户理解
2. 安全：用户只能查询核修改他们可见的字段
3. 数据独立

多表联查
查询每个学生所选修的课程（三张表联查），这个功能在很多的业务中都有使用到，为了简化操作，定义一个视图。

```sql
create view tb_stu_course_view as select s.name student_name , s.no student_no ,

c.name course_name from student s, student_course sc , course c where s.id =

sc.studentid and sc.courseid = c.id;

select * from tb_stu_course_view;
```

### 2. 存储过程

存储过程是事先经过编译并存储在数据库中的一段 SQL 语句的集合，调用存储过程可以简化应用开发人员的很多工作，减少数据在数据库和应用服务器之间的传输，对于提高数据处理的效率是有好处的。
存储过程思想上很简单，就是数据库 SQL 语言层面的代码封装与重用。
**相当于函数的封装**

#### 2.1. 语法

- 创建

```sql
CREATE PROCEDURE 存储过程名称 ([ 参数列表 ])

BEGIN

-- SQL语句

END ;
```

- 调用
  `CALL 名称 ([ 参数 ]);`
- 查看

```sql
SELECT * FROM INFORMATION_SCHEMA.ROUTINES WHERE ROUTINE_SCHEMA = 'xxx'; -- 查询指

定数据库的存储过程及状态信息

SHOW CREATE PROCEDURE 存储过程名称 ; -- 查询某个存储过程的定义
```

- 删除
  `DROP PROCEDURE [ IF EXISTS ] 存储过程名称 ；`

**在命令行中，执行创建存储过程的SQL时，需要通过关键字 delimiter 指定SQL语句的结束符。**

#### 2.2. 变量

- 系统变量
  系统变量 是MySQL服务器提供，不是用户定义的，属于服务器层面。分为全局变量（GLOBAL）、会话变量（SESSION）。

**查看系统变量**

```sql
SHOW [ SESSION | GLOBAL ] VARIABLES ; -- 查看所有系统变量

SHOW [ SESSION | GLOBAL ] VARIABLES LIKE '......'; -- 可以通过LIKE模糊匹配方

式查找变量

SELECT @@[SESSION | GLOBAL].系统变量名; -- 查看指定变量的值
```

**设置系统变量**

```sql
SET [ SESSION | GLOBAL ] 系统变量名 = 值 ;

SET @@[SESSION | GLOBAL].系统变量名 = 值 ;
```

如果没有指定，默认为SESSION（全局变量针对所有会话，会话变量只针对当前会话）

- 用户定义变量
  用户定义变量 是用户根据需要自己定义的变量，用户变量不用提前声明，在用的时候直接用 “@变量名” 使用就可以。其作用域为当前连接。

**赋值**

```sql
方法一
SET @var_name = expr [, @var_name = expr] ... ;

SET @var_name := expr [, @var_name := expr] ... ;

方法二
SELECT @var_name := expr [, @var_name := expr] ... ;

SELECT 字段名 INTO @var_name FROM 表名;
```

**使用**
`SELECT @var_name ;`

- 局部变量
  局部变量 是根据需要定义的在局部生效的变量，访问之前，需要DECLARE声明。可用作存储过程内的局部变量和输入参数，局部变量的范围是在其内声明的BEGIN … END块。
  **声明**
  `DECLARE 变量名 变量类型 [DEFAULT ... ] ;`
  **赋值**

```sql
SET 变量名 = 值 ;

SET 变量名 := 值 ;

SELECT 字段名 INTO 变量名 FROM 表名 ... ;
```

#### 2.3. If

语法结构

```sql
IF 条件1 THEN
.....
ELSEIF 条件2 THEN -- 可选
.....
ELSE -- 可选
.....
END IF;
```

案例

```sql
create procedure p3() -- 创建存储过程p3（函数）

begin

declare score int default 58; -- 创建局部变量score并初始化为58

declare result varchar(10); -- 创建局部变量result并默认初始化为null

if score >= 85 then -- 条件分支

set result := '优秀';

elseif score >= 60 then

set result := '及格';

else

set result := '不及格';

end if;

select result;

end;

call p3(); -- 调用存储过程
```

#### 2.4. 参数

| 类型  | 含义                                         | 备注 |
| ----- | -------------------------------------------- | ---- |
| IN    | 该类参数作为输入，也就是需要调用时传入值     | 默认 |
| OUT   | 该类参数作为输出，也就是该参数可以作为返回值 |      |
| INOUT | 既可以作为输入参数，也可以作为输出参数       |      |

用法

```sql
CREATE PROCEDURE 存储过程名称 ([ IN/OUT/INOUT 参数名 参数类型 ])

BEGIN

-- SQL语句

END ;
```

案例

```sql
create procedure p(in score int,out result varchar(10))
begin
...
end;

-- 调用
call p(18,@result); -- 定义用户变量@result接受返回数据

select @result;
```

#### 2.5. case

选择分支结构（无需赘述）

- 语法1：

```sql
-- 含义： 当case_value的值为 when_value1时，执行statement_list1，当值为 when_value2时，

执行statement_list2， 否则就执行 statement_list

CASE case_value

WHEN when_value1 THEN statement_list1

[ WHEN when_value2 THEN statement_list2] ...

[ ELSE statement_list ]

END CASE;
```

- 语法2：

```sql
-- 含义： 当条件search_condition1成立时，执行statement_list1，当条件search_condition2成

立时，执行statement_list2， 否则就执行 statement_list

CASE

WHEN search_condition1 THEN statement_list1

[WHEN search_condition2 THEN statement_list2] ...

[ELSE statement_list]

END CASE;
```

#### 2.6. while

循环语句（无需赘述）

```sql
-- 先判定条件，如果条件为true，则执行逻辑，否则，不执行逻辑

WHILE 条件 DO

SQL逻辑...

END WHILE;
```

#### 2.7. repeat

有点像do - while

```sql
-- 先执行一次逻辑，然后判定UNTIL条件是否满足，如果满足，则退出。如果不满足，则继续下一次循环

REPEAT

SQL逻辑...

UNTIL 条件

END REPEAT;
```

#### 2.8. loop

简单循环
LEAVE ：配合循环使用，退出循环。（相当于break）

ITERATE：必须用在循环中，作用是跳过当前循环剩下的语句，直接进入下一次循环。（相当于continue）

```sql
[begin_label:] LOOP

SQL逻辑...

END LOOP [end_label];
```

案例

```sql
create procedure p9(in n int)

begin

declare total int default 0;

sum:loop -- 循环体的标签自定义为sum

if n<=0 then

leave sum; -- 满足条件退出循环
end if;

set total := total + n;

set n := n - 1;

end loop sum;

select total;

end;

call p9(100);
```

#### 2.9. 游标

游标（CURSOR）是用来存储查询结果集的数据类型 , 在存储过程和函数中可以使用游标对结果集进行循环的处理。
语法：

- 声明游标
  `DECLARE 游标名称 CURSOR FOR 查询语句 ;`
- 打开游标
  `OPEN 游标名称 ;`
- 获取游标记录
  `FETCH 游标名称 INTO 变量 [, 变量 ] ;`
- 关闭游标
  `CLOSE 游标名称 ;`

相当于将查询的结果筛选后放入该容器中，并在循环中遍历获取记录

#### 2.10. 条件处理程序

条件处理程序（Handler）可以用来定义在流程控制结构执行过程中遇到问题时相应的处理步骤。（异常处理）
语法为：

```sql
DECLARE handler_action HANDLER FOR condition_value [, condition_value]... statement ;
---------------------------------------------------------------------
handler_action 的取值：

CONTINUE: 继续执行当前程序

EXIT: 终止执行当前程序

condition_value 的取值：

SQLSTATE sqlstate_value: 状态码，如 02000

SQLWARNING: 所有以01开头的SQLSTATE代码的简写

NOT FOUND: 所有以02开头的SQLSTATE代码的简写

SQLEXCEPTION: 所有没有被SQLWARNING 或 NOT FOUND捕获的SQLSTATE代码的简写
```

#### 2.11. 存储函数

存储函数是有返回值的存储过程，存储函数的参数只能是IN类型的
语法：

```sql
CREATE FUNCTION 存储函数名称 ([ 参数列表 ])

RETURNS type [characteristic ...]

BEGIN

-- SQL语句

RETURN ...;

END ;
```

characteristic说明

- DETERMINISTIC：相同的输入参数总是返回相同的值
- NO SQL：不包含SQL语句
- READS SQL DATA：包含读取数据的语句但不包含写入数据的语句

与存储过程（函数）相比，存储函数则更适合执行复杂的操作，并且可以修改数据库中的数据。

但是存储函数能实现的其实存储过程（函数）都能实现，而且没有强制要求返回值与指定characteristic特性，更具有普适性，推荐使用存储过程即可。

（个人认为，在业务中不会有人真的只用数据库编程吧，不论是存储过程还是存储函数，使用的几率都不大...）

### 3. 触发器

触发器是与表有关的数据库对象，指在insert/update/delete之前(BEFORE)或之后(AFTER)，触发并执行触发器中定义的SQL语句集合。触发器的这种特性可以协助应用在数据库端确保数据的完整性, 日志记录 , 数据校验等操作 。
使用别名OLD和NEW来引用触发器中发生变化的记录内容，这与其他的数据库是相似的。现在触发器还只支持行级触发，不支持语句级触发。

| 触发器类型 | NEW和OLD                                             |
| ---------- | ---------------------------------------------------- |
| INSERT型   | NEW表示将要或已经新增的数据                          |
| UPDATE型   | OLD表示修改之前的数据，NEW表示将要或已经修改后的数据 |
| DELETE型   | OLD表示将要或者已经删除的数据                        |

语法：

- 创建

```
CREATE TRIGGER trigger_name

BEFORE/AFTER INSERT/UPDATE/DELETE

ON tbl_name FOR EACH ROW -- 行级触发器

BEGIN

trigger_stmt ;//要插入的日志数据

END;
```

- 查看
  `SHOW TRIGGERS ;`
- 删除
  `DROP TRIGGER [schema_name.]trigger_name ; -- 如果没有指定 schema_name，默认为当前数据库 。`

具体案例

```sql
create trigger tb_user_trigger
    after insert on tb_user for each row
begin
    insert into user_logs(id,operation,operate_time,operate_id,operate_parama)
VALUES
    (null,'insert',now(),new.id,concat('插入的数据内容为:id=',new.id,'name=',new.name,',phone=',new.phone,',emall=',new.emall,',profession=',new.profession));
end;
```

## 5.锁

根据粒度不同，MySQL中的锁分为三类：

- 全局锁：锁定数据库所有表
- 表级锁：每次操作锁住一张表
- 行级锁：每次操作锁住对应的行数据

锁是在事务间的，当没有提交事务的时候。其他客户端如果尝试访问有锁（排他）的内容，会被阻塞

### 1.全局锁

整个实例处于只读状态，不可更改。典型的使用场景是作全库的逻辑备份
语法

1. 加全局锁
   `flush tables with read lock ;`
2. 数据备份
   `mysqldump -uroot –p1234 itcast > itcast.sql` ‘1234’表示数据库密码 ‘itcast’是该数据库名称
3. 释放锁
   `unlock tables ;`

存在问题：

- 如果在主库上备份，那么在备份期间都不能执行更新，业务基本上就得停摆。
- 如果在从库上备份，那么在备份期间从库不能执行主库同步过来的二进制日志（binlog），会导致主从延迟。

在InnoDB引擎中，我们可以在备份时加上参数 --single-transaction 参数来完成不加锁的一致性数据备份。
`mysqldump --single-transaction -uroot –p123456 itcast > itcast.sql`
这个参数告诉mysqldump命令在备份过程中使用单个事务，以确保备份的数据是一致的。这样可以避免在备份期间对数据库进行锁定，允许其他用户继续对数据库进行读写操作。

### 2.表级锁

表级锁，每次操作锁住整张表。锁定粒度大，发生锁冲突的概率最高。应用在MyISAM、InnoDB、BDB等存储引擎中。
表级锁分三类

- 表锁
- 元数据锁
- 意向锁

#### 表锁

- 表共享读锁（read lock）
- 表独占写锁（write lock）
- 

**语法**
加锁：lock tables 表名… read/write。
释放锁：unlock tables / 客户端断开连接 。

读锁不会阻塞其他客户端的读，但是会阻塞写。写锁既会阻塞其他客户端的读，又会阻塞其他客户端的写。

#### 元数据锁

MDL加锁过程是系统自动控制，无需显式使用，在访问一张表的时候会自动加上。MDL锁主要作用是维护表元数据的数据一致性，在表上有活动事务的时候，不可以对元数据进行写入操作。为了避免DML与DDL冲突，保证读写的正确性。

元数据可理解为一张表结构，某一张表涉及未提交的事务时是不能修改这张表的结构的。

在MySQL5.5中引入了MDL，当对一张表进行增删改查的时候，加MDL读锁(共享)；当对表结构进行变更操作的时候，加MDL写锁(排他：与任何其他锁互斥，只允许一个事务==独占==地获取资源的访问权限)。

常见的SQL操作所添加的元数据锁

| 对应SQL                                  | 锁类型                                | 说明                                            |
| ---------------------------------------- | ------------------------------------- | ----------------------------------------------- |
| lock tables xxx read/write               | SHARED_READ_ONLY/SHARED_NO_READ_WRITE |                                                 |
| select,select...lock in share mode       | SHARED_READ                           | 与SHARED_READ,SHARED_WRITE兼容，与EXCLUSIVE互斥 |
| insert,update,delete,select...for update | SHAREAD_WRITE                         | 与SHARED_READ,SHARED_WIRTE兼容，与EXCLUSIVE互斥 |
| alter table ...                          | EXCLUSIVE                             | 与其他的MDL都互斥                               |

查看元数据锁的加锁情况
`select object_type,object_schema,object_name,lock_type,lock_duration from performance_schema.metadata_locks ;`

#### 意向锁

为了避免DML在执行时，加的行锁与表锁的冲突（在加表锁的时候其中有行锁，事务可能被阻塞），在InnoDB中引入了意向锁，使得表锁不用检查每行数据是否加锁，使用意向锁来减少表锁的检查

其实就相当于在加锁的时候给整个表打上一个标签，后续加表锁的时候告知是否可加，而无需再逐行判断行锁是否存在

分类：

- 意向共享锁(IS): 由语句select … lock in share mode添加 。 与表锁共享锁(read)兼容，与表锁排他锁(write)互斥。
- 意向排他锁(IX): 由insert、update、delete、select…for update添加 。与表锁共享锁(read)及排他锁(write)都互斥，意向锁之间不会互斥。

**一旦事务提交，意向锁都会自动释放**
查看行锁以及意向锁的加锁情况
`select object_schema,object_name,index_name,lock_type,lock_mode,lock_data from performance_schema.data_locks;`

- 请求意向共享锁（IS锁）：`LOCK TABLES table_name READ`
- 请求意向排他锁（IX锁）：`LOCK TABLES tables_name WRITE`

示例

```sql
-- 开启事务
START TRANSACTION;

-- 请求意向共享锁（IS锁）
LOCK TABLES table_name READ;

-- 执行查询操作
SELECT * FROM table_name;

-- 释放锁
UNLOCK TABLES;

-- 提交事务
COMMIT;
```

### 3.行级锁

行级锁，每次操作锁住对应的行数据。锁定粒度最小，发生锁冲突的概率最低，并发度最高。应用在
InnoDB存储引擎中。
InnoDB的数据是基于索引组织的，行锁是通过对索引上的索引项加锁来实现的，而不是对记录加的
锁。对于行级锁，主要分为以下三类：

- 行锁（Record Lock）：锁定单个行记录的锁，防止其他事务对此行进行update和delete。在RC、RR隔离级别下都支持。
- 间隙锁（Gap Lock）：锁定索引记录间隙（不含该记录），确保索引记录间隙不变，防止其他事务在这个间隙进行insert，产生幻读。在RR隔离级别下都支持。
- 临键锁（Next-Key Lock）：行锁和间隙锁组合，同时锁住数据，并锁住数据前面的间隙Gap。在RR隔离级别下支持。

#### 行锁

InnoDB实现了以下两种类型的行锁：
共享锁（S）：允许一个事务去读一行，阻止其他事务获得相同数据集的排他锁（其他事务可以获取共享锁，进行读操作）。
排他锁（X）：允许获取排他锁的事务更新数据，阻止其他事务获得相同数据集的共享锁和排他锁（确保其他事务不会读取或写入相同的资源，以避免数据不一致）。

| SQL                         | 行锁类型 | 说明                                     |
| --------------------------- | -------- | ---------------------------------------- |
| INSERT...                   | 排他锁   | 自动加锁                                 |
| UPDATE...                   | 排他锁   | 自动加锁                                 |
| DELETE...                   | 排他锁   | 自动加锁                                 |
| SELECT(正常)                | 不加锁   |                                          |
| SELECT...LOCK IN SHARE MODE | 共享锁   | 需要手动在SELECT之后加LOCK IN SHARE MODE |
| SELECT...FOR UPDATE         | 排他锁   | 需要手动在SELECT之后加FOR UPDATE         |

默认情况下，InnoDB在 REPEATABLE READ事务隔离级别运行，InnoDB使用 next-key 锁进行搜索和索引扫描，以防止幻读。
针对唯一索引进行检索时，对已存在的记录进行等值匹配时，将会自动优化为行锁。
InnoDB的行锁是针对于索引加的锁，**不通过**索引条件检索数据，那么InnoDB将对表中的所有记录加锁，此时 就会升级为表锁。

#### 间隙锁&临键锁

1. 间隙锁（Gap Lock）：
   - 间隙锁是在索引范围内的间隙（两个索引键之间的空间）上设置的锁。
   - 间隙锁的作用是防止其他事务在间隙内插入新的索引键，从而保证了范围查询的一致性。
   - 间隙锁可以防止幻读的问题，因为它会锁定范围内的间隙，阻止其他事务在该范围内插入新的数据。
2. 临界锁（Next-Key Lock）：
   - 临界锁是在索引范围内的索引键上设置的锁，同时也锁定了索引键之间的间隙。
   - 临界锁的作用是防止其他事务在范围内插入新的索引键或修改已有的索引键，从而保证了范围查询的一致性。
   - 临界锁可以防止幻读的问题，因为它会锁定范围内的索引键和间隙，阻止其他事务在该范围内插入新的数据或修改已有的数据。

区别：

- 间隙锁只锁定了索引范围内的间隙，而临界锁不仅锁定了索引范围内的索引键，还锁定了索引键之间的间隙。
- 临界锁比间隙锁的范围更广，因为它同时锁定了索引键和间隙。
- 临界锁提供了更严格的一致性，因为它不仅防止了插入新数据，还防止了修改已有数据。

默认情况下，InnoDB在 REPEATABLE READ事务隔离级别运行，InnoDB使用 next-key 锁进行搜索和索引扫描，以防止幻读。
索引上的等值查询(唯一索引)，给不存在的记录加锁时, 优化为间隙锁 。
索引上的等值查询(非唯一普通索引)，向右遍历时最后一个值不满足查询需求时，next-key lock 退化为间隙锁。
索引上的范围查询(唯一索引)–会访问到不满足条件的第一个值为止。

注意：间隙锁唯一目的是防止其他事务插入间隙。间隙锁可以共存，一个事务采用的间隙锁不会阻止另一个事务在同一间隙上采用间隙锁。（在有间隙锁的情况下，如id=3 到 id =5 之间，此时插入id=4的数据就会阻塞）

对于索引上的范围查询，会将范围内的索引键上都上锁（临键锁）

## 6.InnoDB引擎

### 1.逻辑存储结构

![InnoDB](E:\桌面\MyNoteBook\MySql\InnoDB.png)

- A: 表空间：表空间是InnoDB存储引擎逻辑结构的最高层。一个mysql实例可以对应多个表空间，用于存储记录、索引等数据。
- B:段：分为数据段（Leaf node segment）、索引段（Non-leaf node segment）、回滚段（Rollback segment），InnoDB是索引组织表，数据段就是B+树的叶子节点， 索引段即为B+树的非叶子节点。段用来管理多个Extent（区）。
- C:区：表空间的单元结构，每个区的大小为1M。 默认情况下， InnoDB存储引擎页大小为16K， 即一个区中一共有64个连续的页。
- D:页：是InnoDB 存储引擎磁盘管理的最小单元，每个页的大小默认为 16KB。为了保证页的连续性，InnoDB 存储引擎每次从磁盘申请 4-5 个区。
- E: 行：InnoDB 存储引擎数据是按行进行存放的。
  在行中，默认有两个隐藏字段：
  Trx_id：每次对某条记录进行改动时，都会把对应的事务id赋值给trx_id隐藏列。
  Roll_pointer：每次对某条引记录进行改动时，都会把旧的版本写入到undo日志中，然后这个隐藏列就相当于一个指针，可以通过它来找到该记录修改前的信息。

### 2.架构

#### 内存结构

1. Buffer Pool
   InnoDB存储引擎基于磁盘文件存储，访问物理硬盘和在内存中进行访问，速度相差很大，为了尽可能弥补这两者之间的I/O效率的差值，就需要把经常使用的数据**加载**到缓冲池中，避免每次访问都进行磁盘I/O。
   在InnoDB的缓冲池中不仅缓存了索引页和数据页，还包含了undo页、插入缓存、自适应哈希索引以及InnoDB的锁信息等等。
   缓冲池 Buffer Pool，是主内存中的一个区域，里面可以缓存磁盘上经常操作的真实数据，在执行增删改查操作时，先操作缓冲池中的数据（若缓冲池没有数据，则从磁盘加载并缓存），然后再以一定频率刷新到磁盘，从而减少磁盘IO，加快处理速度。
   缓冲池以Page页为单位，底层采用链表数据结构管理Page。根据状态，将Page分为三种类型：
   • free page：空闲page，未被使用。
   • clean page：被使用page，数据没有被修改过。
   • dirty page：脏页，被使用page，数据被修改过，也中数据与磁盘的数据产生了不一致。
2. Change Buffer
   Change Buffer，更改缓冲区（针对于非唯一二级索引页），在执行DML语句时，如果这些数据Page没有在Buffer Pool中，不会直接操作磁盘，而会将数据变更存在更改缓冲区 Change Buffer中，在未来数据被读取时，再将数据合并恢复到Buffer Pool中，再将合并后的数据刷新到磁盘中。
3. Adaptive Hash Index
   自适应hash索引，用于优化对Buffer Pool数据的查询。MySQL的innoDB引擎中虽然没有直接支持hash索引，但是给我们提供了一个功能就是这个自适应hash索引。InnoDB存储引擎会监控对表上各索引页的查询，如果观察到在特定的条件下hash索引可以提升速度，则建立hash索引，称之为自适应hash索引。**自适应哈希索引，无需人工干预，是系统根据情况自动完成。**
4. Log Buffer
   日志缓冲区，用来保存要写入到磁盘中的log日志数据（redo log 、undo log），默认大小为 16MB，日志缓冲区的日志会定期刷新到磁盘中。如果需要更新、插入或删除许多行的事务，增加日志缓冲区的大小可以节省磁盘 I/O。
   参数：
   innodb_log_buffer_size：缓冲区大小
   innodb_flush_log_at_trx_commit：日志刷新到磁盘时机，取值主要包含以下三个：
   1: 日志在每次事务提交时写入并刷新到磁盘，默认值。
   0: 每秒将日志写入并刷新到磁盘一次。
   2: 日志在每次事务提交后写入，并每秒刷新到磁盘一次。

#### 磁盘结构

1. System Tablespace
   系统表空间是更改缓冲区的存储区域。
2. File-Per-Table Tablespaces
   如果开启了innodb_file_per_table开关 ，则每个表的文件表空间包含单个InnoDB表的数据和索引 ，并存储在文件系统上的单个数据文件中。
   开关参数：innodb_file_per_table ，该参数默认开启。
3. General Tablespaces
   通用表空间，需要通过 CREATE TABLESPACE 语法创建通用表空间，在创建表时，可以指定该表空间。
   创建表空间
   `CREATE TABLESPACE ts_name ADD DATAFILE 'file_name' ENGINE = engine_name;`
   创建表时指定表空间
   `CREATE TABLE xxx ... TABLESPACE ts_name;`
4. Undo Tablespaces
   撤销表空间，MySQL实例在初始化时会自动创建两个默认的undo表空间（初始大小16M），用于存储undo log日志。
5. Temporary Tablespaces
   InnoDB 使用会话临时表空间和全局临时表空间。存储用户创建的临时表等数据。
6. Doublewrite Buffer Files
   双写缓冲区，innoDB引擎将数据页从Buffer Pool刷新到磁盘前，先将数据页写入双写缓冲区文件中，便于**系统异常时恢复数据**。
7. Redo Log
   重做日志，是用来实现事务的持久性。该日志文件由两部分组成：
   重做日志缓冲（redo log buffer）以及重做日志文件（redo log）,前者是在内存中，后者在磁盘中。当**事务提交之后**会把所有修改信息都会存到该日志中, 用于在刷新脏页到磁盘时,发生错误时, 进行数据恢复使用。

#### 后台线程

1. Master Thread
   核心后台线程，负责调度其他线程，还负责将缓冲池中的数据异步刷新到磁盘中, 保持数据的一致性，还包括脏页的刷新、合并插入缓存、undo页的回收 。

2. IO Thread
   在InnoDB存储引擎中大量使用了AIO来处理IO请求, 这样可以极大地提高数据库的性能，而IO Thread主要负责这些IO请求的回调。

   | 线程类型             | 默认个数 | 职责                         |
   | -------------------- | -------- | ---------------------------- |
   | Read thread          | 4        | 负责读操作                   |
   | Write thread         | 4        | 负责写操作                   |
   | Log thread           | 1        | 负责将日志缓冲区刷新到磁盘   |
   | Insert buffer thread | 1        | 负责将写缓冲区内容刷新到磁盘 |

   

3. Purge Thread
   主要用于回收事务已经提交了的undo log，在事务提交之后，undo log可能不用了，就用它来回收。

4. Page Cleaner Thread
   **协助** Master Thread 刷新脏页到磁盘的线程，它可以减轻 Master Thread 的工作压力，减少阻塞。

### 3.事务原理

#### 3.1.事务基础

特性：
• 原子性（Atomicity）：事务是不可分割的最小操作单元，要么全部成功，要么全部失败。
• 一致性（Consistency）：事务完成时，必须使所有的数据都保持一致状态。
• 隔离性（Isolation）：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立环境下运行。
• 持久性（Durability）：事务一旦提交或回滚，它对数据库中的数据的改变就是永久的。

原子性、一致性、持久化，实际上是由InnoDB中的两份日志来保证的，一份是redo log日志，一份是undo log日志。 而持久性是通过数据库的锁，加上MVCC来保证的。

![事务](E:\桌面\MyNoteBook\MySql\事务.png)

#### 3.2.redo log

重做日志，记录的是事务提交时数据页的物理修改，是用来实现事务的持久性。

该日志文件由两部分组成：重做日志缓冲（redo log buffer）以及重做日志文件（redo log file）,前者是在内存中，后者在磁盘中。当事务提交之后会把所有修改信息都存到该日志文件中, 用于在刷新脏页到磁盘,发生错误时, 进行数据恢复使用。

当对缓冲区的数据进行增删改之后，会首先将操作的数据页的变化，**记录**在redo log buffer中。在事务提交时，会将redo log buffer中的数据**刷新**到redo log磁盘文件中。
过一段时间之后，如果刷新缓冲区的脏页到磁盘时，发生**错误**，此时就可以借助于redo log进行数据恢复，这样就保证了事务的持久性。 而如果脏页成功刷新到磁盘或者涉及到的数据已经落盘，此时redolog就没有作用了，就可以删除了，所以存在的两个redolog文件是循环写的。

**为什么不把脏页直接刷新回磁盘？**
因为操作数据一般都是随机读写磁盘的，而不是顺序读写磁盘。 而redo log在往磁盘文件中写入数据，由于是日志文件，所以都是顺序写的。顺序写的效率，要远大于随机写。 这种先写日志的方式，称之为 WAL（Write-Ahead Logging）。

#### 3.3.undo log

回滚日志，用于记录数据被修改前的信息 , 作用包含两个 : 提供回滚(保证事务的原子性) 和MVCC(多版本并发控制) 。

undo log和redo log记录物理日志不一样，它是**逻辑**日志。可以认为当delete一条记录时，undo log中会记录一条对应的insert记录，反之亦然，当update一条记录时，它记录一条对应相反的update记录。当执行rollback时，就可以从undo log中的逻辑记录读取到相应的内容并进行回滚。
Undo log销毁：undo log在事务执行时产生，事务提交时，并不会立即删除undo log，因为这些日志可能还用于MVCC。
Undo log存储：undo log采用段的方式进行管理和记录，存放在前面介绍的 rollback segment回滚段中，内部包含1024个undo log segment。

### 4.MVCC

#### 4.1.基础概念

1). 当前读
读取的是记录的最新版本，读取时还要保证其他并发事务不能修改当前记录，会对读取的记录进行加锁。对于我们日常的操作，如：select … lock in share mode(共享锁)，select …for update、update、insert、delete(排他锁)都是一种当前读。
2). 快照读
简单的select（不加锁）就是快照读，快照读，读取的是记录数据的可见版本，有可能是历史数据，不加锁，是非阻塞读。
• Read Committed：每次select，都生成一个快照读。
• Repeatable Read：开启事务后第一个select语句才是快照读的地方。
• Serializable：快照读会退化为当前读。
（**在事务开始的时候锁定一个状态，后面读取的都是这个状态的数据**）
具体来说，快照读的特点如下：

1. 读取一致性的数据快照：快照读会在事务开始时创建一个数据快照，并在整个事务期间使用该快照来读取数据。这意味着无论其他事务如何修改数据，快照读始终读取的是事务开始时的数据状态，保证了读取的一致性。
2. 不阻塞写操作：与其他隔离级别不同，快照读不会对并发的写操作产生阻塞。这意味着其他事务可以并发地修改数据，而不会对快照读产生影响。
3. 读取操作不会加锁：快照读不会对读取的数据进行加锁，因此不会对其他事务的读取和写入操作产生阻塞。这提高了并发性能，允许多个事务同时进行读取操作。
   3). MVCC
   全称 Multi-Version Concurrency Control，多版本并发控制。指**维护一个数据的多个版本**，使得读写操作没有冲突，快照读为MySQL实现MVCC提供了一个非阻塞读功能。MVCC的具体实现，还需要依赖于数据库记录中的三个隐式字段、undo log日志、readView。

#### 4.2.隐藏字段

InnoDB会为我们的表数据自动添加隐藏字段

| 隐藏字段    | 含义                                                         |
| ----------- | ------------------------------------------------------------ |
| DB_TRX_ID   | 最近修改事务ID，记录插入这条记录或最后一次修改该记录的事务ID。 |
| DB_ROLL_PTR | 回滚指针，指向这条记录的上一个版本，用于配合undo log，指向上一个版本. |
| DB_ROW_ID   | 隐藏主键，如果表结构没有指定主键，将会生成该隐藏字段.        |

上述的前两个字段是肯定会添加的， 是否添加最后一个字段DB_ROW_ID，得看当前表有没有主键，如果有主键，则不会添加该隐藏字段。

#### 4.3.undolog

回滚日志，在insert、update、delete的时候产生的便于数据回滚的日志。
当insert的时候，产生的undo log日志只在回滚时需要，在事务提交后，可被立即删除。
而update、delete的时候，产生的undo log日志不仅在回滚时需要，在**快照读**时也需要，不会立即被删除（保证数据读取的一致性）。

**版本链**
不同事务或相同事务对同一条记录进行修改，会导致该记录的undolog生成一条记录版本链表，链表的头部是最新的旧记录，链表尾部是最早的旧记录。通过回滚指针来确定回滚到哪个版本。

![版本链](E:\桌面\MyNoteBook\MySql\版本链.png)

#### 4.4.readview

ReadView（读视图）是 快照读 SQL执行时MVCC提取数据的依据，记录并维护系统当前活跃的事务（未提交的）id。
其四个核心字段

| 字段           | 含义                                               |
| -------------- | -------------------------------------------------- |
| m_ids          | 当前活跃的事务ID集合                               |
| min_trx_id     | 最小活跃事务ID                                     |
| max_trx_id     | 预分配事务ID，当前最大事务ID+1(因为事务ID是自增的) |
| creator_trx_id | ReadView创建者的事务ID                             |

版本连数据的访问规则
trx_id表示当前undolog版本链对应的事务ID

| 条件                           | 是否可以访问                    | 说明                                       |
| ------------------------------ | ------------------------------- | ------------------------------------------ |
| trx_id==creator_trx_id         | 可以访问该版本                  | 成立，说明数据是当前这个事务更改的         |
| trx_id<min_trx_id              | 可以访问该版本                  | 成立，说明事务已经提交                     |
| trx_id>max_trx_id              | 不可访问该版本                  | 成立，说明该事务是在ReadView生成后才开启的 |
| min_trx_idM=trx_id<=max_trx_id | 如果trx_id不在m_ids中，可以访问 | 成立，说明数据已经提交                     |

（注：事务ID是自增的，当前事务版本ID如果比最小活跃事务ID还小，说明早已在最小活跃事务**之前提交了当前事务版本**，因此可以访问）

不同的隔离级别，生成ReadView的时机不同：

- READ COMMITTED ：在事务中每一次执行快照读时生成ReadView。
- REPEATABLE READ：仅在事务中第一次执行快照读时生成ReadView，后续复用该ReadView。

#### 4.5.原理分析

1. Read Uncommitted（读未提交）：最低的隔离级别，允许一个事务读取另一个事务尚未提交的数据。这可能导致脏读（Dirty Read）问题，即读取到未经验证的数据。
2. Read Committed（读已提交）：在该隔离级别下，一个事务只能读取已经提交的数据。这可以避免脏读问题，但可能导致不可重复读（Non-repeatable Read）问题，即同一事务内多次读取同一数据时，得到的结果可能不一致。
3. Repeatable Read（可重复读）：在该隔离级别下，一个事务在执行期间多次读取同一数据时，得到的结果保持一致。这可以避免不可重复读问题，但可能导致幻读（Phantom Read）问题，即同一事务内多次查询时，得到的结果集可能不一致。
4. Serializable（串行化）：最高的隔离级别，要求事务串行执行，确保事务之间没有并发冲突。这可以避免脏读、不可重复读和幻读问题，但会降低并发性能。

因此在RC隔离级别下，每次快照读都会生成一个ReadView，保证读取的数据已提交

而RR是可重复读，只需要在第一次执行快照读的时候生成ReadView就可，通过复用该ReadView实现重复读取同一版本数据

## 7.MySQL管理

### 7.1.系统数据库

| 数据库             | 含义                                                         |
| ------------------ | ------------------------------------------------------------ |
| mysql              | 存储MySQL服务器正常运行所需要的各种信息(时区、主从、用户、权限等) |
| Information_schema | 提供了访问数据库元数据的各种表和视图，包含数据库、表、字段类型及访问权限等 |
| performance_schema | 为MySQL服务器运行时状态提供了一个底层监控功能，主要用于收集数据库服务器性能参数 |
| sys                | 包含了一系列方便 DBA 和开发人员利用 performance_schema性能数据库进行性能调优和诊断的视图 |

### 7.2.常用工具

#### mysql

mysql客户端工具提供一系列语法

```sql
语法：
    mysql    [options]  [database]
选项：
    -u,--user=name    # 指定用户名
    -p,--password[=name]  # 指定密码
    -h,--host=name  # 指定服务器IP或域名
    -P,--port=port  # 指定连接端口
    -s,--execute=name  # 执行SQL语句并退出
```

-e选项可以在Mysql客户端执行SQL语句，而不用连接到MySQL数据库再执行，对于一些批处理脚本，这种方式尤其方便。
示例：
`mysql -uroot –p123456 db01 -e "select * from stu";`

#### mysqladmin

mysqladmin 是一个执行管理操作的客户端程序。可以用它来检查服务器的配置和当前状态、创建并删除数据库等。

```sql
语法：
    mysqladmin  [options]  command...
选项：
    -u,--user=name    # 指定用户名
    -p,--password[=name]  # 指定密码
    -h,--host=name  # 指定服务器IP或域名
    -P,--port=port  # 指定连接端口
```

示例：

```sql
mysqladmin -uroot –p1234 drop 'test01';

mysqladmin -uroot –p1234 version;
```

#### mysqlbinlog

由于服务器生成的二进制日志文件以二进制格式保存，所以如果想要检查这些文本的文本格式，就会使用到mysqlbinlog 日志管理工具。

```sql
语法：
    mysqlbinlog  [options]  log-filesl  log-files2...
选项：
    -d,--database=name  # 指定数据库名称，只列出指定的数据库相关操作
    -o,--offset=#       # 忽略掉日志中的前n行命令
    -r,--result-file=name  # 将输出的文本格式日志输出到指定文件
    -s,--short-form  # 显示简单格式，省略掉一些信息
    --start-datatime=date1  --stop-datetime=date2  # 指定日期间隔内的所有日志
    ==start-position=pos1  --stop-position=pos2  # 指定位置间隔内的所有日志
```

示例：

- 查看binlog.000008这个二进制文件中的数据信息`mysqlbinlog binlog.000008`

#### mysqlshow

mysqlshow 客户端对象查找工具，用来很快地查找存在哪些数据库、数据库中的表、表中的列或者索引。

```sql
语法：
    mysqlshow [options] [db_name [table_name [col_name]]]
选项：
    --count  # 显示数据库及表的统计信息 (数据库，表 均可以不指定)
    -i       # 显示指定数据库或者指定表的状态信息
示例：
   # 查询test库中每个表中的字段数及行数
   mysqlshow -uroot -p2143 test --count
   #查询test库中book表的详细情况
   mysglshow -uroot -p2143 test book --count
```

示例：

- 查询每个数据库的表的数量及表中记录的数量`mysqlshow -uroot -p1234 --count`
- 查看数据库db01中的course表的信息`mysqlshow -uroot -p1234 db01 course --count`

#### mysqldump

mysqldump 客户端工具用来备份数据库或在不同数据库之间进行数据迁移。备份内容包含创建表，及插入表的SQL语句。

```sql
语法：
    mysqldump [options] db_name [tables]
    mysqldump [options] --database/-B dbl [db2 db3...]
    mysqldump [options] --all-databases/-A
连接选项 :
    -u, --user=name  # 指定用户名
    -p, --password[=name]  # 指定密码
    -h, --host=name  # 指定服务器ip或域名
    -P， --port=#  # 指定连接端口
输出选项:
    --add-drop-database  # 在每个数据库创建语句前加上 drop database
    --add-drop-table  # 语句在每个表创建语句前加上 drop table 语句 ，默认开启 ;不开启 (--skip-add-drop-table)
    -n, --no-create-db  # 不包含数据库的创建语句
    -t，--no-create-info  # 不包含数据表的创建语句
    -d--no-data  # 不包含数据
    -T，--tab=name  # 自动生成两个文件:一个.sq1文件，创建表结构的语句;一个.txt文件，数据文件
```

示例：

- 备份db01数据库`mysqldump -uroot -p1234 db01 > db01.sql`
- 备份db01数据库中的表数据，不备份表结构(-t)`mysqldump -uroot -p1234 -t db01 > db01.sql`
- 将db01数据库的表的表结构与数据分开备份(-T)`mysqldump -uroot -p1234 -T /root db01 score`(要存放在MySQL信任的目录下才行)

#### mysqlimport/source

1). mysqlimport
mysqlimport 是客户端数据导入工具，用来导入mysqldump 加 -T 参数后导出的文本文件。

```sql
语法：
    mysqlimport [options]  db_name  textfilel  [textfile2...]
示例：
    mysqlimport -uroot -p1234 test /tmp/city.txt
```

2). source
如果需要导入sql文件,可以使用mysql中的source 指令 :

```sql
语法：
    source /root/xxxxx.sql
```


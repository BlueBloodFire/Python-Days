# Days-15

## SQL的详解

SQL主要分为四类：

1. DDL(数据定义语言)
   
   主要用于创建、删除、修改数据库中的对象，核心关键字包括```create```,```drop```,```alter```

2. DML(数据操作语言)

   主要负责数据的插入、删除和更新，核心关键字包括```insert```,```delete```,```update```

3. DQL(数据查询语言)
   
   主要负责数据查询，核心关键字为```select```

4. DCL(数据控制语言)

   通常用于授予和召回权限，核心关键字为```grant```,```revoke```

### DDL

这里我们可以用下面的例子来展示选课系统数据库的创建

```
-- 创建数据库school
  
   create database school

-- 使用数据库school
   
   use school

-- 创建学院表
create table tb_college
(
col_id int unsigned auto_increment comment '编号',
col_name varchar(50) not null comment '名称',
col_intro varchar(500) default '' comment '介绍',
primary key (col_id)
) engine=innodb auto_increment=1 comment '学院表';

-- 创建学生表
create table tb_student
(
stu_id int unsigned not null comment '学号',
stu_name varchar(20) not null comment '姓名',
stu_sex boolean default 1 not null comment '性别',
stu_birth date not null comment '出生日期',
stu_addr varchar(255) default '' comment '籍贯',
col_id int unsigned not null comment '所属学院',
primary key(stu_id),
constraint fk_student_col_id foreign key(col_id) references tb_college (col_id)
) engine=innodb comment '学生表';

-- 创建教师表
create table tb_teacher
(
tea_id int unsigned not null comment '工号',
tea_name varchar(20) not null comment '姓名',
tea_title varchar(10) default '助教' comment '职称',
col_id int unsigned not null comment '所属学院',
primary key (`tea_id`),
constraint fk_teacher_col_id foreign key (col_id) references tb_college (col_id)
) engine=innodb comment '老师表';

-- 创建课程表
create table tb_course
(
cou_id int unsigned not null comment '编号',
cou_name varchar(50) not null comment '名称',
cou_credit int not null comment '学分',
tea_id int unsigned not null comment '授课老师',
primary key (cou_id),
constraint fk_course_tea_id foreign key (tea_id) references tb_teacher (tea_id)
) engine=innodb comment '课程表';

-- 创建选课记录表
create table tb_record
(
rec_id bigint unsigned auto_increment comment '选课记录号',
stu_id int unsigned not null comment '学号',
cou_id int unsigned not null comment '课程编号',
sel_date date not null comment '选课日期',
score decimal(4,1) comment '考试成绩',
primary key (rec_id),
constraint fk_record_stu_id foreign key (stu_id) references tb_student (stu_id),
constraint fk_record_cou_id foreign key (cou_id) references tb_course (cou_id),
constraint uk_record_stu_cou unique (stu_id, cou_id)
) engine=innodb comment '选课记录表';
```

+ 以上可得，创建数据库，表通常用```create```
+ 设置属性的时候，若不清楚要定义的数据类型，可用```? data types```
+ 保存很大的字符串可以用```TEXT```类型，保存很大的字节串可以用```BLOB```类型
+ 保存定点数应该使用```DECIMAL```类型，如果要保存时间日期，```DATETIME```类型优于```TIMESTAMP```类型，因为前者能表示的时间日期范围更大

### DML

这里我们可以展示如何用DML语言来修改数据库

```
-- 进入数据库school
   
   use school;

-- 插入学院数据

   insert into tb_college values('编号','名称','介绍')

-- 插入学生数据

   insert into tb_student values('学号','姓名','性别','出生日期','籍贯'，'所属学院')

```

上面的```insert```语句使用了批处理的方式来插入数据，这种做法插入数据的效率比较高

### DQL
```
select * from # 选择读取全部数据

select ··· from ··· # 选择从某某读取数据 

select ··· from ··· where ··· # 选择从某某的限制条件读取数据

select ··· from ··· where ··· and ··· #选择从某某的两个以上(包括两个)的限制条件读取数据

select ··· from ··· where ··· or ··· #选择从某某的这个条件或那个条件读取数据

select ··· from ··· where ··· is null #选择值为空的数据

select ··· from ··· where ··· is not null #选择值不为空的数据
```

相关聚合函数例如```avg()```,```sum()```,```min()```以及操作字符UNION的使用等可参考此地址

[MySQL教程](https://www.runoob.com/mysql/mysql-tutorial.html)

### DCL

```
create user *** # 创建用户

grant select,insert on *** to *** # 将搜索，插入权限授予用户

revoke insert on *** from *** # 从用户收回插入权限

```

## 索引

[MySQL教程](https://www.runoob.com/mysql/mysql-tutorial.html)

### 索引的设计原则

1. 最适合索引的列是出现在WHERE子句和连接子句中的列

2. 索引列的基数越大(取值多、重复值少),索引的效果就越好

3. 使用前缀索引可以减少索引占用的空间，内存中可以缓存更多的索引

4. 索引不是越多越好，虽然索引加速了读操作，但是写操作都会变得更慢，因为数据的变化会导致索引的更新，就如同书籍章节的增删需要更新目录一样

5. 使用InnoDB存储引擎时，表的普通索引都会保存主键的值，所以主键要尽可能选择较短的数据类型，这样可以有效地减少索引占用的空间，提升索引的缓存效果

## 视图

视图是关系型数据库中将一组查询指令构成的结果集组合成可查询的数据表的对象，简单地说，视图就是虚拟的表，但与数据表不同的是，数据表是一种实体结构，而视图是一种虚拟结构，可以将视图理解为保存在数据库中被赋予名字的SQL语句

使用视图有如下好处：

1. 可以将实体数据表隐藏起来，让外部程序无法得知实际的数据结构，让访问者可以使用表的组成部分而不是整个表，降低数据库被攻击的风险

2. 在大多数的情况下视图是只读的(更新视图的操作通常都有诸多的限制)，外部程序无法直接透过视图修改数据

3. 重用SQL语句，将高度复杂的查询包装在视图表中，直接访问该视图即可取出需要的数据，也可以将视图视为数据表进行连接查询

4. 视图可以返回与实体数据表不同格式的数据，在创建视图的时候可以对数据进行格式化处理

```
-- 从表中选择数据创建视图

   create view *** as
   select *** as ***
   from *** group by ***

-- 基于已有的视图创建视图

   create view *** as
   select *** from *** natural join ***(原有视图的名称)

-- 使用视图

   select * from ***(视图) order by *** desc

-- 删除视图
   
   drop view ***
```

以下不能更新的视图

1. 使用了聚合函数(```sum```,```min```.```max```,```avg```,```count```等)，```DISTINCT```,```GROUP```,```BY```,```HAVING```,```UNION```,```UNION ALL```的视图

2. ```select```中包含了子查询的视图

3. ```from```子句中包含了一个不能更新的视图的视图

4. ```where```子句的子查询引用了```from```子句的表的视图

视图的规则和限制

1. 视图可以嵌套，可以利用从其他视图中检索的数据来构造一个新的视图，视图也可以和表一起使用

2. 创建视图时可以使用```order by```子句，但如果从视图中检索数据时也使用了```order by```，那么该视图中原先的```order by```会被覆盖

3. 视图无法使用索引，也不会激发触发器的执行

## 窗口函数

窗口函数可以理解为记录的集合，窗口函数也就是在满足某种条件的记录集合上执行的特殊函数，对于每条记录都要在此窗口内执行函数。窗口函数与聚合函数的主要区别在于聚合函数是将多条记录聚合为一条记录，窗口函数是每条记录都会执行，执行后记录条数不会变，窗口函数不仅仅是几个函数，它是一套完整的语法，函数只是该语法的一部分。

```
<窗口函数> over (partition by <用于分组的列名> order by <用户排序的列名>)
```

窗口函数可以放以下两种函数

1. 专用窗口函数
   ```lead```,```lag```,```first_value```,```last_value```,```rank```,```dense_rank```,```row_number```等

2. 聚合函数
   ```sum```,```avg```,```max```,```min```,```count```

## 数据完整性

1. 实体完整性 - 每个实体都是独一无二的

   - 主键(primary key) / 唯一约束(unique)

2. 引用完整性(参照完整性) - 关系中不允许引用不存在的实体

   - 外键(foreign key)

3. 域完整性 - 数据是有效的
  
   - 数据类型及长度

   - 非空约束(not null)

   - 默认值约束(default)

   - 检查约束(check)

## 数据一致性

1. 事务：一系列对数据库进行读/写的操作

2. 事务的ACID特性
   - 原子性：事务作为一个整体被执行，包含在其中对数据库的操作要么全部被执行，要么都不执行

   - 一致性：事务应确保数据库的状态从一个一致状态转变为另一个一致状态

   - 隔离性：多个事务并发执行时，一个事务的执行不应影响其他事务的执行

   - 持久性：已被提交的事务对数据库的修改应该永久保存在数据库中

3. MySQL中的事务操作
   
   - 开启事务环境
     ```
     start transaction
     ```

   - 提交事务
     ```
     commit
     ```

   - 回滚事务
     ```
     rollback
     ```

4. 查看事务隔离级别
```
show variables like ***(事务名)
```

5. 修改事务隔离级别
```
set session transaction isolation level read committed
```


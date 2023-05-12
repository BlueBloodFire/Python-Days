# Days-16

## Python程序接入MySQL数据库

这一过程主要演示如何通过Python程序操作MySQL数据库实现数据持久化操作

### 建库建表
```
-- 创建名为hrs的数据库并指定默认的字符集
create database hrs default character set utf8mb4;

-- 切换到hrs数据库
use hrs;

-- 创建部门表
create table tb_dept
(
dno int not null comment '编号',
dname varchar(10) not null comment '名称',
dloc varchar(20) not null comment '所在地',
primary key (dno)
);

-- 插入4个部门
insert into tb_dept values 
    (10, '会计部', '北京'),
    (20, '研发部', '成都'),
    (30, '销售部', '重庆'),
    (40, '运维部', '深圳');

-- 创建员工表
create table tb_emp
(
eno int not null comment '员工编号',
ename varchar(20) not null comment '员工姓名',
job varchar(20) not null comment '员工职位',
mgr int comment '主管编号',
sal int not null comment '员工月薪',
comm int comment '每月补贴',
dno int not null comment '所在部门编号',
primary key (eno),
constraint fk_emp_mgr foreign key (mgr) references tb_emp (eno),
constraint fk_emp_dno foreign key (dno) references tb_dept (dno)
);

-- 插入14个员工
insert into tb_emp values 
    (7800, '张三丰', '总裁', null, 9000, 1200, 20),
    (2056, '乔峰', '分析师', 7800, 5000, 1500, 20),
    (3088, '李莫愁', '设计师', 2056, 3500, 800, 20),
    (3211, '张无忌', '程序员', 2056, 3200, null, 20),
    (3233, '丘处机', '程序员', 2056, 3400, null, 20),
    (3251, '张翠山', '程序员', 2056, 4000, null, 20),
    (5566, '宋远桥', '会计师', 7800, 4000, 1000, 10),
    (5234, '郭靖', '出纳', 5566, 2000, null, 10),
    (3344, '黄蓉', '销售主管', 7800, 3000, 800, 30),
    (1359, '胡一刀', '销售员', 3344, 1800, 200, 30),
    (4466, '苗人凤', '销售员', 3344, 2500, null, 30),
    (3244, '欧阳锋', '程序员', 3088, 3200, null, 20),
    (3577, '杨过', '会计', 5566, 2200, null, 10),
    (3588, '朱九真', '会计', 5566, 2500, null, 10);
```

### 接入MySQL
```
pip install pymysql cryptography
```

1. 创建连接

2. 获取游标

3. 发出SQL

4. 如果执行```insert```,```delete```,```update```操作，需要根据实际情况提交或回滚事务

5. 关闭连接

## NoSQL入门

### NoSQL数据库的分类

| 类型 | 部分代表 | 特点 |
| :---: | :---: | :---: |
| 列族数据库 | HBase Cassandra Hypertable | 按列存储数据，方便存储结构化和半结构化数据，方便做数据压缩，对针对某一列或者某几列的查询有非常大的I/O优势，适合于批量数据处理和即时查询
| 文档数据库 | MongoDBCouchDBElasticSearch | 文档数据库一般用类JSON格式存储数据，存储的内容是文档型的，这样就有机会对某些字段建立索引，实现关系数据库的某些功能，但不提供对参照完整性和分布事务的支持
| KV数据库 | DynamoDBRedisLevelDB | 可以通过key快速查询到其value，有基于内存和基于磁盘两种实现方案
| 图数据库 | Neo4JFlockDBJanusGraph | 使用图结构进行语义查询的数据库，它使用节点、边和属性来表示和存储数据。图数据库从设计上，就可以简单快速地检索难以在关系系统中建模地复杂层次结构
| 对象数据库 | db4oVersant | 通过类似面向对象语言的语法操作数据库，通过对象的方式存取数据

### Redis

一种基于键值对的NoSQL数据库，提供了对多种数据类型的支持；并提供持久化机制，能够将内存中的数据保存到硬盘上，在发生意外状况时数据也不会丢失，此外Redis还支持键过期、地理信息运算、发布订阅、事务、管道、Lua脚本扩展等功能。

#### Redis特点

1. 读写性能极高，并且有丰富的特性(发布/订阅、事务、通知等)

2. 支持数据的持久化(RDB和AOF两种方式)，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用

3. 支持多种数据类型，包括：string、hash、list、set、zset、bitmap、hyperloglog等

4. Redis支持主从复制(实现读写分析)以及哨兵模式(监控master是否宕机)

5. Redis支持分布式集群，可以很容易的通过水平扩展来提升系统的整体性能

6. Redis基于TCP提供的可靠传输服务进行通信，很多编程语言都提供了Redis客户端支持

#### Redis应用场景

1. 高速缓存 - 将不常变化但又经常被访问的热点数据放到Redis数据库，可以大大降低关系型数据库的压力，从而提升系统的响应性能

2. 排行榜 - 很多网站都有排行榜功能，利用Redis中的列表和有序集合可以非常方便的构造各种排行榜系统

3. 商品秒杀/投票点赞 - Redis提供了对计数操作的支持，网站上常见的秒杀、点赞等功能都可以利用Redis的计数器通过+1或-1的操作来实现，从而避免了使用关系型数据库的```update```操作

4. 分布式锁 - 利用Redis可以跨多台服务器实现分布式锁(类似于线程锁，但是能够被多台机器上的多个线程或进程共享)的功能，用于实现一个阻塞式操作

5. 消息队列 - 消息队列和高速缓存一样，是一个大型网站不可缺少的基础服务，可以实现业务解耦和非实时业务削峰等特性

#### Redis的安装和配置

[安装教程](https://blog.csdn.net/weixin_43883917/article/details/114632709)

#### Redis命令的使用

[Redis命令](https://blog.csdn.net/weixin_43883917/article/details/114632709)

#### 在Python程序中使用Redis
```
pip3 install redis
```
进入Python交互式环境，使用```redis```三方库来操作Redis
```
>>> import redis
>>>
>>> client = redis.Redis(host='127.0.0.1', port=6379, password='yourpass')
>>>
>>> client.set('username', 'admin')
True
>>> client.hset('student', 'name', 'luohao')
1
>>> client.hset('student', 'age', 40)
1
>>> client.keys('*')
[b'username', b'student']
>>> client.get('username')
b'admin'
>>> client.hgetall('student')
{b'name': b'luohao', b'age': b'40'}
```
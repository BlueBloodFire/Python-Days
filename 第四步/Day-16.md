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
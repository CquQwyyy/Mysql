####  单行处理函数

----



> 三张表格:==emp,dept,salgrade==

1. #####  ==lower:小写==

   `Mysql: select lower(ename) as enam from emp;`

2. #####  ==upper:大写==

   `Mysql: select upper(ename) as enam from emp;`

3. #####  ==substr:截取字段==

   `Mysql: select substr(ename,1,3) as ename from emp;`

4. ##### ==length:字段长度==

    `Mysql: select length(ename) as ename from emp;;`

5. #####  ==trim:去空格==

   `Mysql: select ename from emp where ename=trim('  smith');`

6. #####  ==str_to_date:将字符串类型(varchar)输出指定格式的日期类型(date)==

   `Mysql: select str_to_date('2023/07/22','%Y/%m/%d') as date;`

7. ##### ==date_format:按照指定格式格式化表内日期字段数据==

   `Mysql: select ename,date_format(hiredate,'%W %D %M %Y') as hiredate from emp;`

8. ##### ==round：四舍五入==

   `Mysql: select round(sal) from emp;`

9. ##### ==rand():生成随机数==

   `Mysql: select round(rand()*100) from emp;`

10. ##### ==case...when...then:当某个字段达到要求就换成then后面的内容==

   ```mysql
   select
       ->  case deptno
       ->  when '20' then 'Reserch'
       ->  when '10' then 'Accounting'
       ->  when '30' then 'Sales'
       ->  when '40' then 'Operations'
       ->  end as 'dept'
       ->  from emp;
   ```

11. ##### ==ifnull:当计算结果为null时指定结果==

​	   `Mysql: select ename,ifnull(sal+comm,0) as sal from emp;;`

-----------------

#### 分组函数（多行处理函数）

---



1. ##### ==count（计算有多少行数据）==

   `Mysql: select job,count(*) from emp group by job;`

2. #####  ==sum（对指定字段求和）==

   `Mysql: select sum(sal) from emp;`

3. #####  ==avg（对指定字段求平均值）==

   `Mysql: select job,round(avg(sal)) from emp group by job;`

4. #####  ==max（求指定字段最大值）==

   `Mysql: select job,max(sal) from emp group by job;`

5. ##### ==min（求指定字段最小值）==

   `Mysql: select job,min(sal) from emp group by job;`

6. ==注意事项:==

   1. 分组函数计算式自动忽略null;

   2. count(*)和count(具体字段)的区别.

      count(具体字段)统计字段下所有不为null的元素总数

      count(*)统计表中的总行数
   
   3. 分组函数不能用在where语句中：因为分组函数必须先分组后才能使用，而group by的执行顺序在where后面;
   
      如：
   
      ```mysql
      select ename,sal from emp where sal > min(sal);
      ```
   
      可以写成:
   
      ```mysql
      select ename,sal from emp where sal > (select min(sal) from emp);
      ```
   
      

----------

#### 分组查询

----------



```mysql
select

	......

from

	......

group by

	......
```

例：查询每个岗位的平均薪资

```mysql
select job,round(avg(sal)) as avgsal from emp group by job;
```

==PS：当select语句中存在group by 语句的情况下，select后面的字段只能跟：参加分组的字段和分组函数；==

例：查询每个部门，不同工作岗位的最高薪资;

```mysql
select deptno,job,max(sal) from emp group by deptno,job;
```

例:   查询每个部门最高薪资，找出薪资大于3000的;

```mysql
select deptno,max(sal) from emp where sal>3000 group by deptno;
select deptno,max(sal) from emp group by depton having max(sal)>3000;
```

两种方式优先使用where筛选，效率高;

-------

#### 查询去重(关键字：distinct)

-------



```mysql
select distinct job from emp;
```

例:查询岗位数量;

```mysql
select count(distinct job) from emp;
```

----

#### 连接查询

-----------



==连接类别:==

1. 内连接:

      1.1等值连接

   例：找出员工姓名和所在部门名称;

   ```mysql
   	   /*sql 92写法*/
   	   select
       -> 		e.ename,d.dname
       -> from
       -> 		emp e,dept d
       -> where
       -> 		e.deptno=d.deptno;
       	/*sql 99写法*/
       	select
       ->  	e.ename,d.dname
       -> from
       ->  	emp e
       -> join
       ->  	dept d
       -> on
       ->  	e.deptno=d.deptno;
   ```

      1.2非等值连接

   例：找出员工薪资等级，显示员工名称，薪资，薪资等级；

   ```mysql
   	   select
       ->  	e.ename,e.sal,s.grade
       -> from
       ->  	emp e
       -> join
       ->  	salgrade s
       -> on
       ->  	e.sal between s.losal and s.hisal;
   ```

      1.3自连接

   例:  找出员工的上级领导，显示员工名和领导名;

   ```mysql
   	   select
       ->  	e.ename,t.ename
       -> from
       -> 		emp e
       -> join
       -> 		emp t
       -> on
       -> 		e.mgr=t.empno;
   ```

2. 外连接

      2.1左外连接（左连接）

   ```mysql
   	   select
       -> 		e.ename,d.dname
       -> from
       -> 		dept d
       -> left join
       -> 		emp e
       -> on
       -> 		e.deptno=d.deptno;
   ```

      2.2右外连接（右连接）：显示right join后面表的全部数据，无论是否匹配，产生了主次关系。右边的表为主；

   ```mysql
   	   select
       -> 		e.ename,d.dname
       -> from
       -> 		emp e
       -> right join
       -> 		dept d
       -> on
       -> 		e.deptno=d.deptno;
   ```

3. 全连接（用的少）:两张表全部展现出来

----

#### 子查询

----



概念:select里面嵌套select语句，被嵌套的select语句称为子查询；

子查询可以出现在以下位置:

```mysql
select
	....(select)
from
	....(select)
where
	....(select)
```

1.where语句子查询

例:找出比最低工资高的员工姓名和工资

```mysql
select ename,sal from emp where sal>(select min(sal) from emp);
```

2.from语句子查询,可以将查询出来的结果当成一张临时表

例：找出每个岗位的平均工资的薪资等级

```mysql
	   select
    -> 		t.*,grade
    -> from
    -> 		salgrade s
    -> join
    -> 		(select job,avg(sal) avgsal from emp group by job) t
    -> on
    -> 		t.avgsal between s.losal and s.hisal;
```

3.select后面的子查询

----

#### Union合并查询结果集

---



例:找出工作岗位是manager和salesman的员工

```mysql
	   select 
	->   	ename,job from emp where job='salesman'
    -> union
    -> select
    ->		ename,job from emp where job='manager';
```

==PS:union合并时，要求两个结果集列数相同==

-----

#### 关键字:limit starIndex,length

---



例:查询员工工资，按降序排列，显示前五名

```mysql
	   select
    -> 		ename,sal
    -> from
    -> 		emp
    -> order by
    -> 		sal desc
    -> --取前5名=limit 0,5;
    -> limit 5;
```

分页显示

第pageNo页:	limit (pageNo-1)*pageSize,pageSize

sql语句执行顺序总结: 

```mysql
select
		....
from
		....
where
		....
group by
		....
having
		....
order by
		....
limit
		....
```

----

#### 表的创建：CREATE

-----



```mysql
语法格式

CREATE TABLE 表名（

	字段名1 数据类型 DEFAULT 默认值，

	字段名2 数据类型

）;
```

常见数据类型

varchar：变长字符串，根据实际数据长度动态分配空间

char：固定长度，无论实际多长都分配固定空间

int：数字中的整型

bigint：长整型

float：单精度浮点数

double：双精度浮点数

date：段日期类型

datetime：长日期类型

clob：字符大对象，存储文章等长字符对象

blob：二进制大对象，存储图片，声音，视频等流数据

-----

#### 表的删除：DROP

----



```mysql
语法格式

DROP TABLE 表名;

DROP TABLE IF EXISTS 表名; --即使表不存在也不会报错
```

----

#### 数据插入：INSERT

----



```mysql
语法格式

INSERT INTO STUDENT(no,name,sex,age,emails)
VALUES(
    -> 1,'bob','男',18,'11614654',
    -> 2,'jack','男',18,'11614654',
    -> 3,'mary','女',20,'11614654',
    -> 4,'koko','女',18,'11614654'
);
```

-----

#### now()函数

---



获取系统当前时间

-----

#### 表的修改：UPDATE

----



```mysql
语法格式

UPDATE STUDENT SET name = 'JACK' WHERE no = 1;
```

==不加条件限制的话会改变表的全部数据==

-----

#### 数据删除：DELETE

----



```mysql
语法格式

DELETE FROM STUDENT WHERE no = 1;
```

==不加条件会删除全部数据==

这种删除方式比较慢;

删除原理：只是删除表中数据（一个一个删除），数据在硬盘上的空间不释放，可以回滚；



TRUNCATE：物理删除，无法回滚，效率高

-----------

#### 表的复制（可以当作一种备份）

----



```mysql
语法格式

CREATE TABLE EMP2 AS SELECT * FROM EMP;
```

----

#### 字段约束

----



约束类型

非空约束：not null

唯一性约束：unique

主键约束：primary key（PK）

外键约束：foreign key（FK）

检查约束：check（mysql不支持）



unique支持联合字段唯一：unique（字段1，字段2）；表示字段1+字段2唯一



primary key

如果一个字段被not null 和 unique约束后，这个字段自动变为主键约束（MySQL中）；

主键是表中数据的唯一标识，一张表主键约束只能添加一个；

```mysql
语法格式

	   create table student(
           --定义主键并且主键自增
    -> 		id int primary key auto_increment,
    -> 		name varchar(255)
    -> );
    
    --表级约束写法
       create table student(
    -> 		id int,
    -> 		name varchar(255),
    ->      primary key(id)
    -> );
    --复合主键写法,不建议使用
       create table student(
    -> 		id int,
    -> 		name varchar(255),
    ->      emails varchar(255),
    ->      primary key(id,emails)
    -> );
```



外键约束

student表

--------------------------------------------------------------------------------

no（pk） name cno（fk）

-------------

class表

----------------

classno（pk） classname

----------------------------

学生和班级是多对一关系，可以将classno放到student表中成为一个字段，定义成外键约束。

定义后，cno的值只能为classno中的；

PS：表之间存在关系时，删除时先删子表，再删父表。

​										   创建表时，先创建父表，在创建子表。

​									       删除数据时，先删子表，再删父表。

​									       插入数据时，先插入父表，再插入子表。

语法格式

```mysql
create table student(
            --定义主键并且主键自增
    -> 		id int primary key auto_increment,
    -> 		name varchar(255),
	->		cno int,
			--定义外键约束,关联的表为class，通过cno和classno字段
	->		foreign key(cno) references class(classno)
    -> );
```

----------------------

#### 事务

------------------------------------



提交事务：commit

回滚事务：rollback

开启事务：start transaction（开启事务，关闭mysql的默认提交事务）

Mysql默认是自动提交事务的，每执行一条DML语句，就提交一次；

事务的特性：

A：原子性，事务是最小的工作单元，不可再分。

C：一致性，所有事务要求，在同一个事务中，所有操作必须同时成功，或者同时失败。

I：隔离性，A事务和B事务之间具有隔离性。

D：持久性，事务最终结束的一个保障，事务提交，就相当于将没有保存到硬盘上的数据保存到硬盘上。



隔离级别

读未提交：read uncommitted（最低隔离级别）

​				   事务A可以读取到事务B未提交的数据（存在脏读现象Dirty read）

读已提交：read committed

​				   事务A只能读取到事务B提交之后的数据

​				   解决了脏读现象（存在不可重复读取数据）

可重复读：repeatable read

​				   事务A开启之后，不管是多久，每一次在事务A中读取到的数据都是一样的。即使事务B已经将数据修	                 				   改，并且提交，事务A读取到的数据还是没有发生改变。

​                   解决了不可重复读取问题（存在幻读，每一次读取的数据都是幻象，不够真实）

​					Mysql默认这个级别；

串行化：serializable（最高隔离级别）

​				    效率最低。解决所有问题。

​					串行化执行数据，不能并发。

```mysql
设置全局隔离级别

set global transaction isolation level read uncommitted;

--查看隔离级别
select @@transaction_isolation;
```

==read uncommitted==

-----------------------------------------------------------------------------------------

```mysql
事务A                                                               事务B

start transaclation										start transaction

select * from t_user(没数据)						insert into t_user values('zhangsan')

select * from t_user

+----------+
| name     |
+----------+
| zhangsan |
+----------+

有数据，读取到了事务B未提交的数据
```

-----------------



==read committed==

---------------

```mysql
事务A																 事务B																

start transaction										start transaction
select * from t_user(没数据)                                                                     																								  				                      insert into t_user values(''zhangsan)

select * from t_user(没数据)      						  commit;									   

select * from t_user

+----------+
| name     |
+----------+
| zhangsan |
+----------+

有数据，只能读取到事务B提交的数据,事务B每提交一次，事务A查询的结果对应就会改变
```



---------------



==repeatable read==

----------------------

```mysql
事务A                                                            事务B

												 insert into t_user values('zhangsan')

select * from t_user

+----------+
| name     |
+----------+
| zhangsan |
| zhangsan |
| zhangsan |
| zhangsan |
| zhangsan |
+----------+

start transaction									  start transaction						

													  delete from t_user

													  commit

select * from t_user

+----------+
| name     |
+----------+
| zhangsan |
| zhangsan |
| zhangsan |
| zhangsan |
| zhangsan |
+----------+

在A事务开启期间，A读取的B的数据是不变的，无论B事务怎么改变，只要A事务不结束，结果就一直是B事务开启前的结果；
```

------------



==serializable==

--------------

```mysql
事务A                                                          事务B
start transaction                                      start transaction
insert into t_user values('zhangsan')			       
													   select * from t_user
													   -
事务B在A事务没结束之前只能等待，无法查询到数据；
```

-------------

#### 索引

-------------



索引是在数据库表的字段上添加的，是为了提高查询效率。

一张表的一个字段可以添加一个索引，多个字段也可以联合起来添加索引。

索引相当于一本书的目录，为了缩小扫描范围而存在的一种机制。

PS：索引需要先排序，并且这个排序和treeSet数据结构相同。

遵循左小右大，中序遍历的方式。

在数据库中，主键上会自动添加索引；在Mysql中，一个字段如果由unique约束的话，也会自动添加索引。

==索引的创建和删除==

```mysql
语法格式
--创建索引
create index emp_ename_index on emp(ename);

--删除索引
drop index emp_ename_index on emp;
```

--------------

#### 视图

-----------------



==视图的创建和删除==

```mysql
 语法格式
 --创建视图
 create view dept2_view as select * from dept2;
 
 --删除视图
 drop view dept2_view;
```

视图的作用，可以面向视图对象进行增删改查，对视图对象的增删改查会导致原表数据被修改被操作。相当于试图是一个指针对象，指向原表数据；

视图对象的具体作用：将长的查询语句作为一个视图对象创建出来，用来简化sql；

----------

#### 三范式

---------------



第一范式：要求任何一个表必须有主键，每一个字段原子性不可再分。

第二范式：建立在第一范式的基础之上，要求所有非主键字段完全依赖主键，不要产生部份依赖。

第三范式：建立在第二范式的基础之上，要求所有的非主键字段直接依赖主键，不要产生传递依赖。

---------


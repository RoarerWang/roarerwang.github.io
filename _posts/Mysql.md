---
layout:     post                 # 使用的布局（不需要改）
title:      Mysql               # 标题 
subtitle:   mysql使用 sql语句 jdbc           #副标题
date:       2019-08-14              # 时间
author:     王瀚霆                      # 作者
header-img: img/001 (10).jpg   #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - Javase
--- 

# 一 增  
1.增加数据库  
//创建数据库并设置为utf-8编码格式  
creat database 数据库名 default character set utf8 collate utf8_general_ci;  

2.增加表  
``` 
creat table 表名(  
    字段一 int(10)  auto_increment,         //int 自增  
    字段二 varchar(11) default null,        //varchar()  默认为空  
    字段三 data not null,                   //data 设置时间不为空  
    字段四 double unique                    //double 浮点型 唯一约束   
    primary key(字段一)                     //设置字段一为主键  
    primary key(字段,字段)                  //联合主键,两个都一致才违背pk约束  
    //外键 从表中某个字段值，是另一个表的主键值  
    alter table cs add constraint fk_cs_student foreign key(sno) references   student(sno););  
```

3.插入数据  
insert into 表 (列名1,列名2,列名3..) values (值1,值2,值3..); -- 向表中插入某些列  
insert into 表 values (值1,值2,值3..); -- 向表中插入所有列  

4.添加唯一索引 create unique index 索引名 on 表名;   

5.创建视图 create view 视图名 as select语句;   
create view 视图名 as  select d.depno,count(num) as 人数 from department d left join employee e  on d.depno=e.depno group by d.depno;   

6.创建用户create user "用户名"@"localhost" identified by "密码";   

---

# 二 删  
1.删除数据库  drop database 数据库名;  

2.删除表  drop table 表名;  
-- truncate删除所有   
truncate table 表名 直接将表删除，重新建表，auto_increment将置为零，从新开始;   

-- delete删除某条记录 一条一条删除，不清空auto_increment记录数;   
delete from 表名 where 限制条件;  

3.删除主键 alter table 表名 drop primary key;  

4.删除外键 alter table 表名 drop foreign key 外键名;  

5.删除索引 drop index  索引名 on 表名 ;  

6.删除视图 drop view if exists 视图名;   

--- 

# 三 改  
1.数据库改名  
建新库，使用mysqldump工具导出老库数据并导入新库  
```
mysqldump -uuser -ppass -hhost -Pport--single-transaction old_db >old_db.sql
mysql  -uuser -ppass -hhost -Pport -e "create database new_db"
mysql  -uuser -ppass -hhost -Pport  new_db < old_db.sql  
mysql  -uuser -ppass -hhost -Pport -e "drop database old_db"  
```
2.表改名  alter table 旧表名 rename 新表名;   

3.修改列为主键 alter table 表名 modify id int primary key;   

4.字段改值  update 表名 set 字段一=值一,字段二=值二 where 限制条件;    

---

# 四 查  
1.查找数据库  show databases;   

2.查找表  show tables;   

3.查看表结构  desc 表名;  

3.条件查询 select 字段 from 表名 where 条件;  

4.别名查询 select * from 表名 别名;   

5.列运算  select 字段 from 表名;  //可以对字段进行操作不会影响库中数据   

6.**多表查询**     
①1笛卡尔积 (4*14=56) 有很多不符合规范的数据  
select * from 表一,表二;   

②等值连接 (通过外键进行等值连接)  
//因为字段名重名，必须加上表名进行区分  
select * from 表一,表二 where 表一 . 字段 = 表二 . 字段;  

③内连接 inner join 等价于（等值连接）  
select * from dept inner join emp on dept.deptno=emp.deptno;  

④左外连接 (注意，左表和右表的位置，不能随意交换)  
select * from 表一 left join 表二 on 表一 . 字段 = 表二 . 字段;    

⑤右外连接: 符合查询条件的行以及右表不符合条件的行  
select * from 表一 right join 表二 on 表一 . 字段 = 表二 . 字段;  

⑥子查询(嵌套查询)  
-- ① >any(子查询) ：比子查询的结果的其中的一个大,就返回true  
-- ② >all(子查询) : 比子查询的结果所有的都大,才返回true   
-- 1. 查找在研发部且工资不低于测试部任一个员工资的员工的姓名； 
select name from employee where depno=10 and sal>   
all(select sal from employee where depno=20);   

-- 2. 查找比所有研发部雇员收入都高的雇员的姓名；  
select name from employee where sal >   
all(select sal from employee where depno=10)  

⑦联合查询  两张表对象个数不同  
select name,sal from employee where sal>1000;   
select name,sal from employee where depno in(10,20);  

-- 1.union 去重  
select name,sal from employee where sal>1000  
union  
select name,sal,depno from employee where depno in(10,20);  

-- 2.union all 不去重   
select name,sal from employee where sal>1000  
union all  
select name,sal from employee where depno in(10,20);   

⑧自连接(一张表内的关系 可以当成两张表来处理)  
select a.ename 员工,IFNULL(b.ename,"没有领导") 领导from emp a left join emp b on a.empno=b.leader;     

7.查询视图 不建议修改 操作的原数据 select * from 视图名;    

8.查看视图字段信息  desc 视图名;   

9.查看用户 select * from mysql.user;    

---
         
# 五 常用函数 
## 1.常用子句  
-- from子句：				    指定查询数据的表  
-- where子句：			        查询数据的过滤条件  
-- group by子句：		        对匹配where子句的查询结果进行分组  
-- having子句：			        对分组后的结果进行条件限制  
-- order by子句：		        对查询结果结果进行排序，后面跟desc降序或asc升序（默认）  
-- limit子句：			        对查询的显示结果限制数目 开始的索引,数目  
-- like 字句                    模糊查询    
-- group_concat子句             分组连接 帮助显示行转列   
-- if(expr1,expr2,expr3)        第一个表达式成立输出第二个，否则输出第三个    

## 2.常用数学运算  
select 4+5;				             9  
select 4-5;						    -1  
select 10/0;				    	null   
select 10/3;			    		3.3333   
select 10 div 3;		            整除3   
select -10 mod 3;  		            求余   
select mod(10,3);   
select 10%3;    
select ceil(1.23);	            	天花板  
select floor(1.23);	            	地板  
select round(1.23);		            四舍五入    
select pow(2,3);		        	2^3  
select rand();				        随机数  
select sqrt(4);				        开平方根  
select truncate(11.923,2)           小数点后保留几位 小数点前截取为0  
select truncate(11.923,-1)          10   

## 3.常用方法    
select ascii('a'); 					char转ascii码   
select char(97);					ascii码转char   
select bin(9);						十进制转二进制   
select bit_length(12)				返回二进制长度  
select char_length('a');			返回字符长度    
select length('a');					返回字节长度  
select concat("a","b","c");			字符串拼接  
select concat_ws("**","%%");		以指定字符进行连接  
select find_in_set('b','a,b,c');	返回字符位置  
select instr('asdf','sd');			找子字符串，找不到返回0  
select insert('asdf',1,2,'qw');		字符串替换,超过长度返回替换的值  
select replace("ads","ad","zx");	字符串替换	  
select substr("王瀚霆",1,1);		自定义截取字符串  
select left("王瀚霆",1);			从左往右截取  
select right("王瀚霆",1);			从右往左截取  
select upper("asd");				字符串转大写  
select lower("asd");				字符串转小写  
select lpad('asdf',6,"*")			字符串左填充   
select rpad('asdf',6,"*")			字符串右填充   
select trim(" as ");				两端去空格  
select ltrim(" as ");				左边去空格  
select rtrim(" as ");				右边去空格  
select repeat('mysql',3);	    	重复字符串   
select reverse("asc");	    		字符串取反  

## 4.常用日期方法  
select curdate();					返回当前日期  
select curtime();					返回当前时间  
select now();	    				返回当前日期时间  
select WEEKDAY("2019-08-13");		返回数字(1-星期二)  
select week(now(),0);				指定周天从几开始							  				
select DAYNAME(NOW());				返回英文星期    
select DAYOFWEEK(curdate());        返回数字(3-星期二)  
select DAYOFMONTH(NOW());			返回多少号  
select DAYOFYEAR(NOW());			返回天数  
select month(NOW());				获取月份  
select quarter(now());				获取季度  
select year()						获取年份   
select hour(now());					获取小时  
select minute(now());				获取分钟   
select second(now());				获取秒数						  											
select data_add("2019-08-13 10:16:24",interval 1 second);	时间以秒数增加(时间单位都可以)  
select date_format(now(),"%Y%m%d");                         日期格式化(转换成某种格式化字符串)  
select datediff("2018-08-08","2019-08-08");                 两个日期间的天数  

---

# 六 常见五大约束  
not null：非空约束，指定某列不为空   
unique： 唯一约束，指定某列和几列组合的数据不能重复  
primary key：主键约束，指定某列的数据不能重复、唯一  
foreign key：外键，指定该列记录属于主表中的一条记录，参照另一条数据  
check：检查，指定一个表达式，用于检验指定数据  
注意： MySQL不支持check约束，但可以使用check约束，而没有任何效果；  

---

# 七 逻辑运算优先级
比较运算符 > < <= >= = <>           大于、小于、大于(小于)等于、不等于

BETWEEN...AND...                   显示在某一区间的值(含头含尾)
IN(set)                 显示在in列表中的值，例：in(100,200)

LIKE 通配符                模糊查询，Like语句中有两个通配符： %
用来匹配多个字符； first_name like ‘a%’; _ 用来匹配一个字符。例first_name like‘a_’;

IS NULL                 判断是否为空 is null; 判断为空 is not null; 判断不为空

逻辑运算符  and              多个条件同时成立
             or              多个条件任一成立
            not          不成立，例：where not(salary>100);
         regexp          匹配正则表达式 not regexp 不匹配

---

# 八 索引																
select encode("hehe123","www") as 加密,  
decode(encode("hehe123","www"),"www") as 解密 from dual; dual临时表  
select password(11234345);				不可逆   
select last_insert_id from dual;  返回自增最后一个值  
select uuid();					  					

对数据库表中一列或者多列值进行排序的一种结构  
提高查询速度，有好处也有坏处，需要合理建立索引  
有些约束会自动创建索引 primary key, unique   

**特点：**  
是一个单独存储在数据库中的结构，包含对表里面的所有记录的引用指针  
使用索引可以快速的找出在某列或者多列中有这个特定值的行  
所有存储引擎都支持一张表上右16个索引	  

**优点：**  
1.通过创建唯一索引，可以保证表中每一条记录的唯一性  
2.加快查询速度  
3。加速表与表间关联，实现数据参照完整性  
4.减少分组和排序的时间  

**缺点：**  
1.需要单独占据存储空间  
2.创建索引和维护索引要花费大量时间  
3.对表的增删改操有影响，例如插入数据，索引也需要更新  

在存储引擎中支持 普通索引和 唯一索引  
普通索引:mysql 最基本的索引，允许在索引列插入null和重复值  
唯一索引：只允许查询null，但不能插入重复值  


---

# 九 视图  
**意义：**  
一张虚拟的表，没有真实的数据，数据来自于原表;  

**特点：**  
1.简化我们的查询操作，被经常用于查询的结果，当作视图，后期直接查询视图就可以获得结果  
2.提高安全性，通过视图只能让用户看到能看到的字段值，某些值屏蔽。  
3.帮助用户屏蔽真实表的结构。   


**视图和表的区别联系：**
区别：1、视图是已经编译好的sql语句。而表不是    
      2、视图没有实际的物理记录。而表有。  
      3、表是内容，视图是窗口   
      4、表只用物理空间而视图不占用物理空间，视图只是逻辑概念的存在，表可以及时对它进行修改，但视图只能有创建的语句来修改  
      5、表是内模式，视图是外模式  
      6、视图是查看数据表的一种方法，可以查询数据表中某些字段构成的数据，只是一些SQL语句的集合。从安全的角度说，视图可以不给用户接触数据表，从而不知道表结构。  
      7、表属于全局模式中的表，是实表；视图属于局部模式的表，是虚表。   
      8、视图的建立和删除只影响视图本身，不影响对应的基本表。  

联系：视图（view）是在基本表之上建立的表，它的结构（即所定义的列）和内容（即所有数据行）都来自基本表，它依据基本表存在而存在。一个视图可以对应一个基本表，也可以对应多个基本表。视图是基本表的抽象和在逻辑意义上建立的新关系。  

--- 

# 十 用户权限  
mysql账户分为两种：系统用户和普通用户  
授予账户操作库的权限
grant all on j1908_test.* to "wht"@"localhost";  
grant all on mydb.* to "wht"@"localhost";  

---

# 十一 范式  
    意义：   
设计关系数据库时，遵从不同的规范要求，设计出合理的关系型数据库，这些不同的规范要求被称为不同的范式，各种范式呈递次规范，越高的范式数据库冗余越小
目前关系数据库有六种范式：  
第一范式（1NF）、第二范式（2NF）、第三范式（3NF）、巴斯-科德范式（BCNF）、第四范式(4NF）和第五范式（5NF，又称完美范式）。  

1NF: 数据库的每一列都不可再分裂  

2NF: 建立在1NF的基础上，要求实体的属性完全依赖于主关键字。每张表描述一个类。  

3NF: 建立在2NF的基础上，任何非主属性不依赖于其他非主属性(消除传递依赖)  

---

# 十二 jdbc连接数据库  
## 1.导包  
1.创建文件夹把jar包粘贴进来，   
2.build path   
3.再add  

## 2.加载驱动  
Class.forName("com.mysql.jdbc.Driver");  

## 3.连接数据库
Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/数据库名","用户名","密码");

## 4.sql语句  

### 1.insert  
```
String sql="insert into student(id,name,age,sex) values(?,?,?,?) "
PreparedStatement p = conn.prepareStatement(sql);
                p.setInt(1,id);
				p.setString(2, name);
				p.setInt(3,  age);
				p.setString(4, sex);
```

### 2.delete  
```
String sql = "delete from student where id=" + ID;  
PreparedStatement p = conn.prepareStatement(sql);  
```

### 3.update  
```
String sql_3 = "update student set id='" + newId + "' where id='" + oldid + "'";  
PreparedStatement p = conn.prepareStatement(sql);   
```

### 4.select  
```
String sql = "select * from student";
PreparedStatement p = conn.prepareStatement(sql);  
			ResultSet rs = p.executeQuery();  
			while (rs.next()) {  
				int id = rs.getInt("id");  
				String name = rs.getString("name");  
				String sex = rs.getString("sex");  
				int age = rs.getInt("age");  
				System.out.println(id + "  |  " + name + "  |  " + sex + "  |  " + age);  
			}  
```

### 5.sort  
```
sql = "select * from student order by id desc";
PreparedStatement p = conn.prepareStatement(sql);
ResultSet rs = p.executeQuery();  
			while (rs.next()) {  
				int id = rs.getInt("id");  
				String name = rs.getString("name");  
				String sex = rs.getString("sex");  
				int age = rs.getInt("age");  
				System.out.println(id + "  |  " + name + "  |  " + sex + "  |  " + age);  
			}  
```

int nub = p.executeUpdate(); 等于0 没有执行成功    


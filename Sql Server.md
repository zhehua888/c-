# Sql Server



## 三.建表：

```sql
--判断当前表是否存在，U表示是用户建立的表
if exists(select * from sys.objects where name = 'Department' and type = 'U')
	drop table Department
create table Department
(
	DepartmentId int primary key identity(1,1),
	DepartmentName nvarchar(50) not null,
	DepartmentRemart text,
)
-- char:定长，char(10),无论数据是否到10个字节，都占十个字节
--varchar:边长，varchar(10),最多占十个字节
--text:长文本
--char,varchar,text前边都可以加一个n，代表存储unicode字符，对中文友好
--通常字母占一个字符，汉字占两个字符，但在unicode中一视同仁

create table [Rank]
(
	RankId int primary key identity(1,1),
	RankName nvarchar(50) not null,
	RankRemart text,
)

create table Prople
(
	PropleId int primary key identity(1,1),
	--identity：步长，第一个空代表初始值，第二个代表增长值
	PropleName nvarchar(50) not null,
	PropleSex nvarchar(1) default('男') check(PropleSex = '男' or PropleSex = '女'),
	--default：默认值
	--check：添加约束
	PropleBirth smalldatetime not null,
	--smalldatetime：代表在一个小的范围日期中年月日时分秒
	PropleSalary decimal(12,2) check(PropleSalary >= 1000 and PropleSalary <= 1000000) not null,
	--decimal(a,b)：浮点数，a代表总位数，b代表小数后的位数
	ProplePhone nvarchar(15) unique not null,
	--unique 去重，唯一
	PropleAddress nvarchar(200),
	PropleAddTime smalldatetime default(getdate()) not null,
	--getdate 获取当前时间
	--重点：添加外键，员工表中的员工所属的部门和职级与部门表和职级表有关联
	DepartmentId int references Department(DepartmentId) not null,
	RankId int references [Rank](RankId) not null,
	--references:参考查阅，意思是添加员工的部门编号只能参考部门表里的编号，不能随意添加

)
```



## 四.修改表结构和约束维护：

```sql
--修改表结构
--1.添加列
--alter table 表名 add 列名 数据类型
alter table People add PeopleMail varchar(200)

--2.删除列
--alter table 表名 drop column 列名
alter table People drop column PeopleMail
--3.修改列
--alter table 表名 alter column 列名 数据类型
alter table People alter column PeopleAddress nvarchar(250)


--维护约束(添加，删除)
--删除约束
--alter table 表名 drop constraint 约束名
--约束名在表设计中，右键约束
alter table People drop constraint CK__People__PeopleSa__5441852A
--添加约束(check约束)
--alter table 表名 add constraint 约束名 check(表达式)
alter table People add constraint CK__Prople__PeopleSa1 check(PeopleSalary>=1000 and PeopleSalary<=1000000)
--添加约束(主键约束)
--alter table 表名 add constraint 约束名 primary key(列名)
--添加约束(unique约束)
--alter table 表名 add constraint 约束名 unique(列名)
--添加约束(默认值)
--alter table 表名 add constraint 约束名 default 默认值 for 列名
--添加约束(外键约束)
--alter table 表名 add constraint 约束名 foreign key(列名) references 关联表名(列名(一般为主键))
```



## 五.插入数据：

```sql
--向部门表中插入数据
insert into Department(DepartmentName,DepartmentRemart) 
values('市场部','略')
insert into Department(DepartmentName,DepartmentRemart) 
values('软件部','略')
insert into Department(DepartmentName,DepartmentRemart) 
values('企划部','略')
--一次插入多行数据
--union：联合，可一次插入多个数据
insert into Department(DepartmentName,DepartmentRemart) 
select '测试部','略' 
union
select '产品部','略'


--向职级表中插入数据
insert into [Rank](RankName,RankRemart)
select '初级','略' union
select '中级','略' union
select '高级','略'


--向员工表中插入数据
insert into People(PeopleName,PeopleSex,PeopleBirth,PeopleSalary,
PeoplePhone,PeopleAddress,PeopleAddTime,DepartmentId,RankId)
select '张飞','男','1978-5-4',5000,'1723181','中国',getdate(),1,1
```



## 六.数据的修改删除：

```sql
select * from Department
select * from [Rank]
select * from People
--修改
--update 表名 set 字段1=值1,字段2=值2 where 条件
--将所有员工的工资加一千
update People set PeopleSalary = PeopleSalary + 1000
--将员工编号为七的工资加1000
update People set PeopleSalary = PeopleSalary + 1000
where PeopleId = 7
--将软件部(部门编号为2)的人员工资低于15000的调整为15000
update People set PeopleSalary = 15000
where DepartmentId = 2 and PeopleSalary < 15000
--修改刘备的工资是以前的两倍，修改地址为北京
update People set PeopleSalary = 2*PeopleSalary,PeopleAddress = '北京'
where PeopleName = '刘备'

--删除数据
--delete from 表名 where 条件
--删除员工表的所有记录
delete from People 
--删除部门编号为三且工资大大于等于4000的
delete from People where DepartmentId = 3 and PeopleSalary >= 4000

--关于删除(drop,truncate,delete)
drop table Perple --删除表对象，全部
truncate table Prople --删除数据(清除数据)，表对象和结构存在
delete from People --删除所有数据，表对象和结构存在
--delete和truncate区别
--1.truncate删除没有条件，delete可以存在条件
--2.假如自动编号为1，2，3，4
--使用truncate删除数据后，重新存入数据，编号重新开始
--使用delete删除数据后，删除的自动编号将不存在

```



## 七.基本查询：

```sql
--查询所有列所有行
select * from Department
select * from [Rank]
select * from People

--查询指定列(姓名，性别，生日，月薪，电话)
select PeopleName,PeopleSex,PeopleBirth,PeopleSalary,PeoplePhone from People

--查询指定列(姓名，性别，生日，月薪，电话)且显示中文列名
select PeopleName as 姓名,PeopleSex as 性别,
PeopleBirth as 生日,PeopleSalary as 月薪,PeoplePhone as 电话 from People

--查询员工所在的城市(去重)
select distinct(PeopleAddress) from People

--假设准备加工资（20%）,查询出加工资后的员工数据
select PeopleId,PeopleName,PeopleSex,PeopleSalary * 1.2 as PeopleSalary from People

--假设准备加工资（20%）,查询出加工资后与加薪前对比员工数据
select PeopleId,PeopleName,PeopleSex,PeopleSalary as 加薪前工资,PeopleSalary * 1.2 as 加薪后工资 from People

```



## 八.条件查询与排序：

### sql中常用的运算符

```sql
=：等于，比较是否相等及赋值
!=：比较不等于
>：比较大于
<：比较小于
>=：比较大于等于
<=：比较小于等于
IS NULL：比较为空
IS NOT NULL：比较不为空
in：比较是否在其中
like：模糊查询
BETWEEN...AND...：比较是否在两者之间
and：逻辑与（两个条件同时成立表达式成立）
or：逻辑或（两个条件有一个成立表达式成立）
not：逻辑非（条件成立，表达式则不成立；条件不成立，表达式则成立）
```

```sql
--条件查询

select * from People

--查询性别为女的员工信息
select * from People where PeopleSex = '女'

--查询工资大于等于一万元的员工信息
select * from People where PeopleSalary >= 10000

--查询性别为女，工资大于等于一万元的员工信息
select * from People where PeopleSex = '女' and PeopleSalary >= 10000

--查询月薪大于等于一万的员工，或者月薪大于等于八千的女员工
select * from People where PeopleSalary >= 10000 or (PeopleSalary >= 8000 and peopleSex = '女')

--查询出生年月在1978-1-1之后,而且月薪大于等于10000的女员工
select * from People where PeopleBirth >= '1978-1-1'and PeopleSalary >= 10000 and peopleSex = '女'

--查询工资在10000-20000之间
select * from People where PeopleSalary >= 10000 and PeopleSalary <= 20000
select * from People where PeopleSalary between 10000 and 20000

--查询地址在北京或大唐的员工信息
select * from People where PeopleAddress = '北京' or PeopleAddress = '大唐'
select * from People where PeopleAddress in('大唐','北京')

--null：空值
--查询出地址为空的员工
select * from People where PeopleAddress is null

--反之
select * from People where PeopleAddress is not null
```

```sql
--查询出70后的员工
select * from People where PeopleBirth >= '1970-01-01' and  PeopleBirth < '1980-01-01'
select * from People where year(PeopleBirth) between  '1970' and '1979' 

--查询岁数在40-50之间，并且工资在10000-20000
--年龄 = 当前年份 - 生日年份
select * from People where 
(year(getdate()) - year(PeopleBirth)) between 40 and 50
and
PeopleSalary between 10000 and 20000

--查询星座是巨蟹座（6.22-7.22）
select * from People where
(month(PeopleBirth) = 6 and day(PeopleBirth) >= 22)
or
(month(PeopleBirth) = 7 and day(PeopleBirth) <= 22)

--查询工资比李白高的
select * from People where PeopleSalary > 
(select PeopleSalary from People where PeopleName = '李白')

--查询跟李白在一个城市的员工
select * from People where PeopleAddress = 
(select PeopleAddress from People where PeopleName = '李白')

--查询生肖属马的人员
--鼠、牛、虎、兔、龙、蛇、马、羊、猴、鸡、狗、猪
--4  、5 、6 、7 、8、 9、 10、11、0、1、 2 、3
select * from People where (year(PeopleBirth) % 12) = 10;

--查询所有的员工信息，添加一列，显示生肖
select * ,
case year(PeopleBirth) % 12
	when 4 then '鼠'
	when 5 then '牛'
	when 6 then '虎'
	when 7 then '兔'
	when 8 then '龙'
	when 9 then '蛇'
	when 10 then '马'
	when 11 then '羊'
	when 0 then '猴'
	when 1 then '鸡'
	when 2 then '狗'
	when 3 then '猪'
	else ''
end as 生肖
from People 
```



```sql
--排序

--查询员工工资，倒序排序
--asc：升序
--dasc：降序
--默认升序
select * from People order by PeopleSalary desc
select * from People order by PeopleSalary

--查询员工信息，根据名字的长度排序降序
select * from People order by len(PeopleName) desc

--查询工资最高的五个人
select top 5 * from People order by PeopleSalary desc

--查询工资最高的百分之十的员工
select top 10 percent * from People order by PeopleSalary desc
```



## 九.模糊查询：

模糊查询使用like关键字和通配符结合来实现，通配符具体含义如下：

```sql
%：代表匹配0个字符、1个字符或多个字符。
_：代表匹配有且只有1个字符。
[]：代表匹配范围内
[^]：代表匹配不在范围内
```

```sql
--模糊查询
select * from People 
select * from Department
select * from [Rank]
--查询出姓刘的员工信息
select * from People where PeopleName like '刘%'

--查询名字含有昭字的
select * from People where PeopleName like '%昭%'

--查询名字含有昭字的或有月的
select * from People where PeopleName like '%昭%' or PeopleName like '%月%'

--查询姓刘的名字是两个字的
select * from People where PeopleName like '刘_'
select * from People where SUBSTRING(PeopleName,1,1) = '刘' and len(PeopleName) = 2

--名字最后一个字为天
select * from People where PeopleName like '__天'
select * from People where SUBSTRING(PeopleName,len(PeopleName),1) = '天' and len(PeopleName) = 3

--查询电话号码开头为138的信息
select * from People where PeoplePhone like '137%'

--查询出电话号码开头为134的，第四位是1或者2，最后一位为8
select * from People where PeoplePhone like '134[1-2]%8'

--查询出电话号码开头为134的,第四位是2-5，最后一位不是8
select * from People where PeoplePhone like '134[2,3,4,5]%[^8]'
select * from People where PeoplePhone like '134[2-5]%[^8]'

```

## 十.聚合查询：

SQL SERVER中聚合函数主要有：

```sql
count:求数量
max:求最大值
min:求最小值
sum:求和
avg:求平均值
```

```sql
--聚合函数
select * from People
--求员工的总数
select count(*) as 人数 from People

--求最大值，最高工资
select max(PeopleSalary) 最高工资 from People
select * from People where PeopleSalary = (select max(PeopleSalary) 最高工资 from People)

--求最小值，最低工资
select min(PeopleSalary) 最低工资 from People

--工资总和
select sum(PeopleSalary) 总工资 from People

--员工工资平均值
select round(avg(PeopleSalary),2,0) 平均工资 from People
--round(a,b,c) 小数保留 a:原数值 b:保留几位 c:为零 四舍五入，其他的话 舍去
select round(25.555,2,0)

--求数量，工资最大值最小值总和平均值在一行显示
select count(*) 人数,max(PeopleSalary) 最高工资,min(PeopleSalary) 最低工资,
sum(PeopleSalary) 总工资,round(avg(PeopleSalary),2,0) 平均工资 from People

--查询北京地区的数量，最大值最小值总和平均值
select count(*) 人数,max(PeopleSalary) 最高工资,min(PeopleSalary) 最低工资,
sum(PeopleSalary) 总工资,round(avg(PeopleSalary),2,0) 平均工资 from People
where PeopleAddress = '北京'

--查询工资比平均工资高的人员信息
select * from People where PeopleSalary > 
(select round(avg(PeopleSalary),2,0) 平均工资 from People)

--求数量，年龄最大值最小值总和平均值在一行显示
select *,year(getdate())-year(PeopleBirth) from People
--方案一
select count(*) 数量,
max(year(getdate())-year(PeopleBirth)) 最高年龄,
min(year(getdate())-year(PeopleBirth)) 最低年龄,
sum(year(getdate())-year(PeopleBirth)) 总年龄,
avg(year(getdate())-year(PeopleBirth)) 平均年龄
from People

--方案二
--DATEDIFF(a,b,c) 求时间差 a:代表求谁的差 b,c：后减前
select count(*) 数量,
max(DATEDIFF(year,PeopleBirth,getdate())) 最高年龄,
min(DATEDIFF(year,PeopleBirth,getdate())) 最低年龄,
sum(DATEDIFF(year,PeopleBirth,getdate())) 总年龄,
avg(DATEDIFF(year,PeopleBirth,getdate())) 平均年龄
from People

--计算出月薪在5000及 以上的男员工的数量,最大年龄，最小年龄和平均年龄
select count(*) 数量,
max(DATEDIFF(year,PeopleBirth,getdate())) 最高年龄,
min(DATEDIFF(year,PeopleBirth,getdate())) 最低年龄,
avg(DATEDIFF(year,PeopleBirth,getdate())) 平均年龄
from People 
where PeopleSalary >= 5000 and PeopleSex = '男'

--计算出所在地为北京或大唐的女员工的数量,最大年龄，最小年龄和平均年龄
select count(*) 数量,
max(DATEDIFF(year,PeopleBirth,getdate())) 最高年龄,
min(DATEDIFF(year,PeopleBirth,getdate())) 最低年龄,
avg(DATEDIFF(year,PeopleBirth,getdate())) 平均年龄
from People 
where PeopleAddress in('北京','大唐') and PeopleSex = '女'

--年龄比平均年龄高的
select * from People
where 
DATEDIFF(year,PeopleBirth,getdate()) > 
(select avg(DATEDIFF(year,PeopleBirth,getdate())) from People) 
```

## 十一.分组查询：

```sql
--分组查询
--！注意
--简单记：没写在 group by 里、又不是聚合函数的列，绝对不能出现在 select 中，会直接报错。
--group by PeopleAddress
--那 select 里只能出现 PeopleAddress + 各种工资统计聚合，不能随便加别的字段比如 PeopleName、PeopleBirth。

select * from People

--（1）根据员工所在地区分组统计员工人数，员工工资总和，平均工资，最高工资和最低工资
--方案一：使用union
select '北京' 地区,count(*) 人数,max(PeopleSalary) 最高工资,min(PeopleSalary) 最低工资,
sum(PeopleSalary) 总工资,round(avg(PeopleSalary),2,0) 平均工资
from People
where PeopleAddress = '北京'
union
select '武汉' 地区,count(*) 人数,max(PeopleSalary) 最高工资,min(PeopleSalary) 最低工资,
sum(PeopleSalary) 总工资,round(avg(PeopleSalary),2,0) 平均工资
from People
where PeopleAddress = '大唐'
--方案二：使用group by:根据什么进行分组
select PeopleAddress 地区, count(*) 人数,max(PeopleSalary) 最高工资,min(PeopleSalary) 最低工资,
sum(PeopleSalary) 总工资,round(avg(PeopleSalary),2,0) 平均工资
from People
group by PeopleAddress

--（2）根据员工所在地区分组统计员工人数，员工工资总和，平均工资，最高工资和最低工资，
--1975 年及以后出身的员工不参与统计。
select PeopleAddress 地区, count(*) 人数,max(PeopleSalary) 最高工资,min(PeopleSalary) 最低工资,
sum(PeopleSalary) 总工资,round(avg(PeopleSalary),2,0) 平均工资
from People
where PeopleBirth < '1975-1-1'
group by PeopleAddress

--（3）根据员工所在地区分组统计员工人数，员工工资总和，平均工资，最高工资和最低工资，
--要求筛选出员工人数至少在 2 人及以上的记录，并且 1985 年及以后出身的员工不参与统计。
--错误写法
select PeopleAddress 地区, count(*) 人数,max(PeopleSalary) 最高工资,min(PeopleSalary) 最低工资,
sum(PeopleSalary) 总工资,round(avg(PeopleSalary),2,0) 平均工资
from People
where count(*) <= 2 and PeopleBirth < '1975-1-1'
group by PeopleAddress
--正确写法 
--where 是筛原始表数据，不能用 count 这种聚合函数，having 才是筛分组完的数据
--很容易手滑把 count>=2 写到 where 里直接报错
select PeopleAddress 地区, count(*) 人数,max(PeopleSalary) 最高工资,min(PeopleSalary) 最低工资,
sum(PeopleSalary) 总工资,round(avg(PeopleSalary),2,0) 平均工资
from People
where PeopleBirth < '1975-1-1'
group by PeopleAddress
having count(*) >= 2
```

## 十二.多表查询：

```sql
--多表查询
select * from People
select * from Department
--笛卡尔乘积
--此查询结果会将People表的所有数据和Department表的所有数据进行依次排列组合形成新的记录。
--例如People表有10条记录，Department表有3条记录，则排列组合之后查询结果会有10*3=30条记录。
select * from People,Department
--简单多表查询
--查询员工信息，显示部门名称
select * from People,Department
where People.DepartmentId = Department.DepartmentId

--查询员工信息，显示职级名称
select * from People,[Rank]
where People.RankId = [Rank].RankId

--查询员工信息，显示部门名称，显示职级名称
select * from People,[Rank],Department
where People.DepartmentId = Department.DepartmentId
and People.RankId = [Rank].RankId

--内连接查询
--查询员工信息，显示部门名称
--inner 可以省略
select * from People
inner join Department on People.DepartmentId = Department.DepartmentId

--查询员工信息，显示职级名称
select * from People
inner join [Rank] on People.RankId = [Rank].RankId

--查询员工信息，显示部门名称，显示职级名称
select * from People
inner join Department on People.DepartmentId = Department.DepartmentId
inner join [Rank] on People.RankId = [Rank].RankId

--外连接（左连接，右连接，全连接）

--左连接：以左表为主表进行全部数据显示，主外键关系找不到的数据用null代替
--右连接：跟左连接相反
--查询员工信息，显示部门名称
select * from People
left join Department on People.DepartmentId = Department.DepartmentId

--查询员工信息，显示职级名称
select * from People
left join [Rank] on People.RankId = [Rank].RankId

--查询员工信息，显示部门名称，显示职级名称
select * from People
left join Department on People.DepartmentId = Department.DepartmentId
left join [Rank] on People.RankId = [Rank].RankId
     
--全外联：两张表的数据，无论是否符合关系都要显示
select * from People
full join Department on People.DepartmentId = Department.DepartmentId
```

### 自链接：

```sql
--自连接(自己链接自己)
create table Dept
(
    DeptId int Primary key, --部门编号
    DeptName varchar(50), --部门名称
    ParentId int --上级部门编号
)
--一级
insert into Dept(DeptId,DeptName,ParentId) values(1,'软件部',0)
insert into Dept(DeptId,DeptName,ParentId) values(2,'硬件部',0)
--二级
insert into Dept(DeptId,DeptName,ParentId) values(3,'软件研发部',1)
insert into Dept(DeptId,DeptName,ParentId) values(4,'软件测试部',1)
insert into Dept(DeptId,DeptName,ParentId) values(5,'软件实施部',1)
insert into Dept(DeptId,DeptName,ParentId) values(6,'硬件研发部',2)
insert into Dept(DeptId,DeptName,ParentId) values(7,'硬件测试部',2)
insert into Dept(DeptId,DeptName,ParentId) values(8,'硬件实施部',2)

select * from Dept
--相当于给两张相同的表起了不同的名字且A做子表，B做父表
select A.DeptId 部门编号,A.DeptName 部门名称,B.DeptName 上级部门 from 
Dept A 
inner join Dept B on A.ParentId = B.DeptId
```



### 实例：

```sql
--（1）查询出北京地区所有的员工信息，要求显示部门名称以及员工的详细资料(显示中文别名)
select PeopleId 员工编号,People.DepartmentId 部门编号,DepartmentName 部门名称,
PeopleName 员工姓名, PeopleSex 员工性别,PeopleBirth 员工生日,
PeopleSalary 工资,PeoplePhone 电话,PeopleAddress 地址
from People 
left join Department on People.DepartmentId = Department.DepartmentId
where PeopleAddress = '北京' 

--（2）查询出北京地区所有的员工信息，要求显示部门名称，职级名称以及员工的详细资料（显示中文别名）
select PeopleId 员工编号,People.DepartmentId 部门编号,DepartmentName 部门名称,RankName 职级名称,
People.RankId 职级编号,PeopleName 员工姓名, PeopleSex 员工性别,PeopleBirth 员工生日,
PeopleSalary 工资,PeoplePhone 电话,PeopleAddress 地址
from People 
left join Department on People.DepartmentId = Department.DepartmentId
left join [Rank] on People.RankId = [Rank].RankId
where PeopleAddress = '北京' 

--（3）根据部门分组统计员工人数，员工工资总和，平均工资，最高工资和最低工资。
select People.DepartmentId 部门编号,DepartmentName 部门名称, count(*) 员工人数,
sum(PeopleSalary) 工资总和,avg(PeopleSalary) 平均工资,
max(PeopleSalary) 最高工资,min(PeopleSalary) 最低工资
from People
inner join Department on People.DepartmentId = Department.DepartmentId
group by People.DepartmentId,DepartmentName

--（4）根据部门分组统计员工人数，员工工资总和，平均工资，最高工资和最低工资，
--平均工资在10000以下的不参与统计，并且根据平均工资降序排列。
select People.DepartmentId 部门编号,DepartmentName 部门名称, count(*) 员工人数,
sum(PeopleSalary) 工资总和,avg(PeopleSalary) 平均工资,
max(PeopleSalary) 最高工资,min(PeopleSalary) 最低工资
from People
inner join Department on People.DepartmentId = Department.DepartmentId
group by People.DepartmentId,DepartmentName
having avg(PeopleSalary) >= 10000
order by avg(PeopleSalary) desc
--（5）根据部门名称，然后根据职位名称，分组统计员工人数，员工工资总和，平均工资，最高工资和最低工资
select People.DepartmentId 部门编号,DepartmentName 部门名称,
[Rank].RankId 职级编号,RankName 职级名称,
count(*) 员工人数,sum(PeopleSalary) 工资总和,avg(PeopleSalary) 平均工资,
max(PeopleSalary) 最高工资,min(PeopleSalary) 最低工资
from People
inner join Department on People.DepartmentId = Department.DepartmentId
inner join [Rank] on People.RankId = [Rank].RankId
group by People.DepartmentId,DepartmentName,[Rank].RankId,RankName
```


# SQL Server 笔记

## 重启 sql-server
````bash
systemctl status mssql-server # 查看 mssql-server 的运行状态
systemctl restart mssql-server # 重启 mssql-server
systemctl stop mssql-server # 停止 mssql-server 服务
````
## SQL Server 进入单用户模式
````bash
/opt/mssql/bin/sqlservr -m -c -T7806 # -m 参数表示以单用户启动 SQL Server
sqlcmd -S localhost -U SA -P <Password> # 进入 sqlcmd
> sp_addsrvrolemember 'lolimay', 'sysadmin' # 将 lolimay 添加进管理员用户组
> go
systemctl restart mssql-server # 重启 mssql-server 服务
# 或者
 sudo nohup /opt/mssql/bin/sqlservr > /dev/null 2>&1 &
````

## 数据库相关操作
### 新建或删除数据库
````sql
create database experiment2; -- 新建数据库
drop database experiment2; -- 删除数据库
````

### 查询所有用户数据库
````sql
select name,crdate,filename from master.dbo.sysdatabases
	where name NOT IN ('master','model','msdb','tempdb')
````

## 创建表格
如：创建一个 Course 表
````sql
create table Course (
	Cno SMALLINT PRIMARY KEY,
	Cname char(20) UNIQUE,
	Cpno SMALLINT,
	Ccredit SMALLINT
)
````

## 查询（Query）
1. 查询计算机系的学生信息
````sql
select * from Student
	where Sdept='CS'
````
2. 查询年龄在19至23之间的学生信息
````sql
select * from Student
	where Sage>=19 AND Sage<=23
````
3. 查询20岁以下的女生信息
````sql
select * from Student
	where Sage<='20' AND Ssex='女'
````
4. 查询姓名中最后一个字是“伟”的学生信息
````sql
select * from Student
	where Sname like '%伟'
````

    ?> 可以使用 LIKE + % 关键字进行模糊匹配。

5. 查询学号以“E”开头的学生信息
````sql
select * from Student
	where Sno like 'E%'
````
6. 查询学分为2的课程信息
````sql
select * from Course
	where Ccredit=2
````
7. 查询课程名含有“计算机”的课程信息
````sql
select * from Course
	where Cname like '%计算机%'
````
8. 查询“数据库”、“操作系统”、“计算机网络课程”的信息
````sql
select * from Course
	where Cname='数据库' OR Cname='操作系统' OR Cname='计算机网络课程'
````
9. 查询2号课程的选修情况
````sql
select * from Course
	where Cno=2
````
10. 查询某个学生的选课情况(学生学号自定)
````sql
select SC.Sno,Cname from Course,SC
	where SC.Cno=Course.Cno AND SC.Sno='E21714049'
````
11. 查询成绩90分以上的选课情况
````sql
select SC.Sno,Cname from Course,SC
	where SC.Cno=Course.Cno AND SC.Grade>=90
````
12. 查询选课中的2个最高分
````sql
select top 2 Grade from SC
````

    ?> TOP 关键字可以截取排名靠前的一些元组，默认是降序，可以使用 ORDER BY Grade 进行升序。

13. 计算1号课程的最高分、最低分、平均分;
````sql
select MAX(Grade),MIN(Grade),AVG(Grade) from SC
````

    ?> 除了MAX()、MIN()和AVG()这些聚集函数外，还有COUNT()和SUM()等函数。

14. 查询某学生选课的门数、平均分
````sql
select COUNT(Cno), AVG(Grade) from SC
	where Sno='E21714049'
````
15. 分组计算每门课程的最高分、最低分、平均分
````sql
select Cno,MAX(Grade),MIN(Grade), AVG(Grade) from SC
	group by Cno
````

    ?> GROUP BY 可以细化聚集函数的作用对象。

16. 分组计算每个学生的最高分、最低分、平均分
````sql
select Sno,MAX(Grade),MIN(Grade), AVG(Grade) from SC
	group by Sno
````
17. 查询供应工程“J1”零件的供应商信息
````sql
select DISTINCT S.Sno,S.Sname,S.Status,S.City from S,SPJ
	where S.Sno=SPJ.Sno AND SPJ.Jno='J1'
````

    ?> 在 select 和 from 之间加入 DISTINCT 关键字可以去重。
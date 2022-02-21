**1、取得每个部门最高薪水的人员名称**

```sql
SELECT
	e.ename, t.*
FROM 
	emp e
JOIN 
	(SELECT deptno, MAX(sal) maxsal FROM emp GROUP BY DEPTNO) t
ON
	t.deptno = e.deptno AND maxsal = e.sal
```



**2、哪些人的薪水在部门的平均薪水之上**

```sql
SELECT 
	e.ename, e.`SAL`, t.*
FROM 
	emp e
JOIN
	(SELECT deptno, AVG(sal) sal FROM emp GROUP BY deptno) t
ON
	e.`DEPTNO` = t.deptno AND e.sal > t.sal
```



**3、取得部门中（所有人的）平均的薪水等级**

```sql
SELECT 
	AVG(t.grade) grade
FROM
	(SELECT
		s.`GRADE`, e.deptno
	FROM 
		emp e
	LEFT JOIN
		salgrade s
	ON
		e.sal BETWEEN s.`LOSAL` AND s.`HISAL`
	ORDER BY
		e.deptno) t
GROUP BY
	t.deptno
```



**4、不准用组函数（Max ），取得最高薪水**

```sql
SELECT 
	t.*
FROM
	(SELECT ename, sal FROM emp ORDER BY sal DESC) t
LIMIT	
	1
```



**5、取得平均薪水最高的部门的部门编号**

```sql
SELECT
	deptno, avgsal
FROM
	(SELECT deptno, AVG(sal) avgsal FROM emp GROUP BY deptno) t
ORDER BY 
	avgsal DESC
LIMIT 
	1
```



**6、取得平均薪水最高的部门的部门名称**

```sql
SELECT 
	dname, avgsal
FROM
	(SELECT 
		d.dname, e.deptno, AVG(sal) avgsal
	FROM 
		dept d 
	JOIN 
		emp e 
	ON 
		d.`DEPTNO` = e.`DEPTNO`
	GROUP BY
		e.`DEPTNO`)t
ORDER BY 
	t.avgsal DESC
LIMIT
	1
```



**7、求平均薪水的等级最低的部门的部门名称**

```sql
SELECT 	
	 dept.`DNAME`, s.grade, t.*
FROM
	(SELECT 
		d.deptno deptno, AVG(e.sal) sal
	FROM
		emp e
	LEFT JOIN
		dept d
	ON 
		e.`DEPTNO` = d.`DEPTNO` 
	GROUP BY 
		e.`DEPTNO`) t
JOIN
	salgrade s
ON
	t.sal BETWEEN s.`LOSAL` AND s.`HISAL`
LEFT JOIN
	dept
ON
	dept.`DEPTNO` = t.deptno
ORDER BY 
	s.grade
LIMIT
	1
```



**8、取得比普通员工(员工代码没有在 mgr 字段上出现的) 的最高薪水还要高的领导人姓名**

```sql
# 第一步：找出普通员工的最高薪水
非普通员工 （使用） 
SELECT DISTINCT mgr FROM emp WHERE mgr IS NOT NULL
普通员工最高薪水
SELECT MAX(sal) FROM emp WHERE empno NOT IN(SELECT DISTINCT mgr FROM emp WHERE mgr IS NOT NULL)
找出
SELECT ename, sal FROM emp WHERE sal > 
(SELECT MAX(sal) FROM emp WHERE empno NOT IN(SELECT DISTINCT mgr FROM emp WHERE mgr IS NOT NULL))
```



**9、取得薪水最高的前五名员工**

```sql
SELECT 
	ename
FROM 
	emp
ORDER BY
	sal DESC
LIMIT 
	0,5
```



**10、取得薪水最高的第六到第十名员工**

```sql
SELECT 
	ename
FROM 
	emp
ORDER BY
	sal DESC
LIMIT 
	5,5
```



**11、取得最后入职的 5 名员工**

```sql
SELECT 
	ename, hiredate
FROM 
	emp
ORDER BY
	hiredate DESC
LIMIT
	0, 5
```



**12、取得每个薪水等级有多少员工**

```sql
SELECT 
	COUNT(s.grade), s.`GRADE`
FROM
	emp e
LEFT JOIN
	salgrade s
ON
	e.`SAL` BETWEEN s.`LOSAL` AND s.`HISAL`
GROUP BY
	s.`GRADE`
```



**13、面试题：**
	有 3 个表 S(学生表)，C（课程表），SC（学生选课表）
	S（SNO，SNAME）代表（学号，姓名）
	C（CNO，CNAME，CTEACHER）代表（课号，课名，教师）
	SC（SNO，CNO，SCGRADE）代表（学号，课号，成绩）
	问题：
		1，找出没选过“黎明”老师的所有学生姓名。
		2，列出 2 门以上（含2 门）不及格学生姓名及平均成绩。
		3，即学过 1 号课程又学过 2 号课所有学生的姓名。

**14、列出所有员工及领导的姓名**

```sql
SELECT
	e1.ename, e2.`ENAME`
FROM 
	emp e1
LEFT JOIN
	emp e2
ON
	e1.`MGR` = e2.empno
```



**15、列出受雇日期早于其直接上级的所有员工的编号,姓名,部门名称**

```sql
SELECT
	e1.`EMPNO`,e1.ename '员工', d.dname
FROM
	emp e1
JOIN
	emp e2
ON
	e1.`MGR` = e2.`EMPNO`
LEFT JOIN
	dept d
ON
	e1.`DEPTNO` = d.`DEPTNO`
WHERE
	e1.`HIREDATE`<e2.`HIREDATE`
```



**16、 列出部门名称和这些部门的员工信息, 同时列出那些没有员工的部门**

```sql
SELECT
	d.dname, e.*
FROM
	emp e
RIGHT JOIN
	dept d
ON
	e.`DEPTNO` = d.`DEPTNO`
```



**17、列出至少有 5 个员工的所有部门**

```sql
SELECT
	d.dname, d.deptno
FROM
	dept  d
JOIN
	(SELECT 
		COUNT(e.empno) number, e.`DEPTNO` deptno
	FROM
		emp e
	RIGHT JOIN
		dept d
	ON
		d.`DEPTNO` = e.`DEPTNO`
	GROUP BY
		e.`DEPTNO`
	HAVING
		number >= 5) t
ON 
	t.deptno = d.`DEPTNO`
```



**18、列出薪金比"SMITH" 多的所有员工信息**

```sql
SELECT
	ename
FROM
	emp
WHERE
	sal > (SELECT sal FROM emp WHERE ename = 'SMITH')
```



**19、 列出所有"CLERK"( 办事员) 的姓名及其部门名称, 部门的人数**

```sql
SELECT 
	e.deptno,e.ename, d.dname
FROM
	emp e
LEFT JOIN
	dept d
ON
	e.`DEPTNO` = d.`DEPTNO`
WHERE
	e.`JOB` = 'CLERK'
```



**20、列出最低薪金大于 1500 的各种工作及从事此工作的全部雇员人数**

```sql
SELECT 
	MIN(sal) minsal, job, COUNT(job)
FROM 
	emp
GROUP BY
	job
HAVING
	MIN(sal) > 1500
```



**21、列出在部门"SALES"< 销售部> 工作的员工的姓名, 假定不知道销售部的部门编号**

```sql
SELECT
	ename
FROM
	emp
WHERE
	emp.`DEPTNO` = (SELECT deptno FROM dept WHERE dname = 'SALES')
```



**22、列出薪金高于公司平均薪金的所有员工, 所在部门, 上级领导, 雇员的工资等级**

```sql
SELECT
	e1.`DEPTNO`,e1.`ENAME`, e2.`ENAME`,  d.`DNAME`, s.`GRADE`, e1.`SAL`
FROM 
	emp e1
LEFT JOIN
	emp e2
ON
	e1.`MGR` = e2.`EMPNO`
LEFT JOIN
	dept d
ON
	e1.`DEPTNO` = d.`DEPTNO`
LEFT JOIN
	salgrade s
ON
	e1.`SAL` BETWEEN s.`LOSAL` AND s.`HISAL`
WHERE
	e1.`SAL`>(SELECT AVG(sal) FROM emp)
```



**23、 列出与"SCOTT" 从事相同工作的所有员工及部门名称**

```sql
SELECT
	e.ename, d.dname
FROM
	emp e
LEFT JOIN
	dept d
ON 
	e.deptno = d.deptno 
WHERE
	e.job = (SELECT job FROM emp WHERE ename = 'SCOTT') AND e.`ENAME` != 'SCOTT'
```



**24、列出薪金等于部门 30 中员工的薪金的其他员工的姓名和薪金**

```sql
SELECT 
	e.ename, e.sal
FROM
	(SELECT ename, sal FROM emp WHERE deptno != 30) e
JOIN
	(SELECT sal FROM emp WHERE deptno = 30) t
ON
	e.sal = t.sal
```



**25、列出薪金高于在部门 30 工作的所有员工的薪金的员工姓名和薪金. 部门名称**

```sql
SELECT 
	e.ename,e.sal, d.dname,d.deptno
FROM 
	emp e
LEFT JOIN
	dept d
ON
	e.deptno = d.deptno
WHERE 
	e.sal > (SELECT MAX(sal) FROM emp WHERE deptno = 30)
```



**26、列出在每个部门工作的员工数量, 平均工资和平均服务期限**

```sql
SELECT
	COUNT(e.deptno) num, AVG(e.sal) AVG
FROM 
	emp e
GROUP BY
	e.deptno
```



**27、 列出所有员工的姓名、部门名称和工资**

```sql
SELECT 
	e.ename, d.dname, e.sal
FROM 
	emp e
LEFT JOIN
	dept d
ON
	e.deptno = d.`DEPTNO`
```



**28、列出所有部门的详细信息和人数**

```sql
SELECT 
	*
FROM 
	dept d
RIGHT JOIN
	(SELECT 
		COUNT(e.deptno) COUNT, d.deptno dno
	FROM 
		dept d
	LEFT JOIN
		emp e
	ON
		e.`DEPTNO` = d.`DEPTNO`	
	GROUP BY
		d.`DEPTNO`) t
ON
	t.dno = d.`DEPTNO`
```



**29、列出各种工作的最低工资及从事此工作的雇员姓名**

```sql
SELECT 
	e.ename, e.sal, e.job
FROM 
	emp e
JOIN
	(SELECT 
		job job, MIN(sal) minsal
	FROM
		emp
	GROUP BY
		job) t
ON
	e.`SAL` = t.minsal AND e.`JOB` = t.job
```



**30、列出各个部门的 MANAGER( 领导) 的最低薪金**

```sql
SELECT
	MIN(sal), deptno
FROM 
	emp
WHERE 
	job = 'manager'
GROUP BY
	deptno
```



**31、列出所有员工的 年工资, 按 年薪从低到高排序**

```sql
SELECT
	ename,(sal+IFNULL(comm, 0)) * 12 yearsal
FROM 
	emp
ORDER BY
	yearsal
```



**32、求出员工领导的薪水超过3000的员工名称与领导**

```sql
SELECT 
	e1.ename, e2.ename, e2.sal
FROM 
	emp e1
LEFT JOIN
	emp e2
ON
	e1.`MGR` = e2.`empno`
WHERE
	e2.`SAL` > 3000
```



**33、求出部门名称中, 带'S'字符的部门员工的工资合计、部门人数**

```sql
SELECT
	SUM(e.sal), COUNT(e.deptno), e.`DEPTNO`
FROM
	emp e
WHERE
	e.ename LIKE '%s%'
GROUP BY
	e.deptno
```



**34、给任职日期超过 30 年的员工加薪 10%**

```sql
UPDATE emp SET sal = sal * 1.1 WHERE TIMESTAMPDIFF(YEAR, hiredate, NOW()) > 30 
```
















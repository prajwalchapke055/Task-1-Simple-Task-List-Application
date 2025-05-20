# MariaDB for Linux (Ubuntu/Debian)
- $ sudo apt update
- $ sudo apt install mariadb-server mariadb-client -y 
- $ sudo mariadb-secure-installation
- $ sudo systemctl start mariadb
- $ sudo systemctl status mariadb
- $ mariadb -u root -p

##################################################

# SQL Commands

- $ show databases;
- $ create database ditiss;
- $ use ditiss;
- $ show tables;
	- Empty set 

--> Paste the commands given in file into MySQL - CLI 

CREATE TABLE EMP
       (EMPNO INT(4) NOT NULL,
        ENAME varchar(10),
        JOB varchar(9),
        MGR INT(4),
        HIREDATE DATE,
        SAL DECIMAL(7, 2),
        COMM DECIMAL(7, 2),
        DEPTNO INT(2));

INSERT INTO EMP VALUES
        (7369, 'SMITH',  'CLERK',     7902,
        STR_TO_DATE('17-DEC-1980', '%d-%b-%Y'),  800, NULL, 20);
INSERT INTO EMP VALUES
        (7499, 'ALLEN',  'SALESMAN',  7698,
        STR_TO_DATE('20-FEB-1981', '%d-%b-%Y'), 1600,  300, 30);
INSERT INTO EMP VALUES
        (7521, 'WARD',   'SALESMAN',  7698,
        STR_TO_DATE('22-FEB-1981', '%d-%b-%Y'), 1250,  500, 30);
INSERT INTO EMP VALUES
        (7566, 'JONES',  'MANAGER',   7839,
        STR_TO_DATE('2-APR-1981', '%d-%b-%Y'),  2975, NULL, 20);
INSERT INTO EMP VALUES
        (7654, 'MARTIN', 'SALESMAN',  7698,
        STR_TO_DATE('28-SEP-1981', '%d-%b-%Y'), 1250, 1400, 30);
INSERT INTO EMP VALUES
        (7698, 'BLAKE',  'MANAGER',   7839,
        STR_TO_DATE('1-MAY-1981', '%d-%b-%Y'),  2850, NULL, 30);
INSERT INTO EMP VALUES
        (7782, 'CLARK',  'MANAGER',   7839,
        STR_TO_DATE('9-JUN-1981', '%d-%b-%Y'),  2450, NULL, 10);
INSERT INTO EMP VALUES
        (7788, 'SCOTT',  'ANALYST',   7566,
        STR_TO_DATE('09-DEC-1982', '%d-%b-%Y'), 3000, NULL, 20);
INSERT INTO EMP VALUES
        (7839, 'KING',   'PRESIDENT', NULL,
        STR_TO_DATE('17-NOV-1981', '%d-%b-%Y'), 5000, NULL, 10);
INSERT INTO EMP VALUES
        (7844, 'TURNER', 'SALESMAN',  7698,
        STR_TO_DATE('8-SEP-1981', '%d-%b-%Y'),  1500,    0, 30);
INSERT INTO EMP VALUES
        (7876, 'ADAMS',  'CLERK',     7788,
        STR_TO_DATE('12-JAN-1983', '%d-%b-%Y'), 1100, NULL, 20);
INSERT INTO EMP VALUES
        (7900, 'JAMES',  'CLERK',     7698,
        STR_TO_DATE('3-DEC-1981', '%d-%b-%Y'),   950, NULL, 30);
INSERT INTO EMP VALUES
        (7902, 'FORD',   'ANALYST',   7566,
        STR_TO_DATE('3-DEC-1981', '%d-%b-%Y'),  3000, NULL, 20);
INSERT INTO EMP VALUES
        (7934, 'MILLER', 'CLERK',     7782,
        STR_TO_DATE('23-JAN-1982', '%d-%b-%Y'), 1300, NULL, 10);

CREATE TABLE DEPT
       (DEPTNO INT(2),
        DNAME varchar(14),
        LOC varchar(13) );

INSERT INTO DEPT VALUES (10, 'ACCOUNTING', 'NEW YORK');
INSERT INTO DEPT VALUES (20, 'RESEARCH',   'DALLAS');
INSERT INTO DEPT VALUES (30, 'SALES',      'CHICAGO');
INSERT INTO DEPT VALUES (40, 'OPERATIONS', 'BOSTON');

CREATE TABLE BONUS
        (ENAME varchar(10),
         JOB   varchar(9),
         SAL   INT,
         COMM  INT);

CREATE TABLE SALGRADE
        (GRADE INT,
         LOSAL INT,
         HISAL INT);

INSERT INTO SALGRADE VALUES (1,  700, 1200);
INSERT INTO SALGRADE VALUES (2, 1201, 1400);
INSERT INTO SALGRADE VALUES (3, 1401, 2000);
INSERT INTO SALGRADE VALUES (4, 2001, 3000);
INSERT INTO SALGRADE VALUES (5, 3001, 9999);

CREATE TABLE DUMMY
        (DUMMY INT);

INSERT INTO DUMMY VALUES (0);

COMMIT;

#######################################################################3

- $ show tables;

- $ select * from dept;
- $ select * from emp;

- $ select empno as empID,ename,sal,hiredate from emp;  -->  [Here , " as " -> Alias ]
- $ select empno empID,ename,sal,hiredate from emp;

- $ desc emp;

- $ select empno,ename,sal,comm,sal+comm as totalsalary from emp;
- $ select empno,ename,sal,comm,sal+ ifnull(comm,0) as totalsalary from emp;

## ifnull
- $ select empno,ename,sal,comm,sal+ ifnull(comm,0) as totalsalary from emp;

## ORDER BY
- $ select * from emp order by deptno asc,hiredate asc,job asc,ename asc;

## Sub-Query
- $ select * from emp where deptno=(select deptno from dept where dname="SALES");

## GROUP BY
- $ select deptno,job,sum(sal) from emp group by deptno, job order by deptno , job;

## HAVING 
- $ select deptno,job,count(*) from emp where job='CLERK' group by deptno, job having count(*)>1;

## RIGHT OUTER JOIN
- $ select ename,emp.deptno,dept.deptno,dname from emp right outer join dept on emp.deptno = dept.deptno;

## INNER JOIN or EQUI JOIN
- $ select emp.deptno,ename,dept.deptno,dname from dept,emp where dept.deptno=emp.deptno order by dept.deptno;

########################################################################################

📘 EMPLOYEE TABLE QUERIES
🔹 1. Display the Highest, Lowest, Total & Average salary for each Department

SELECT 
  deptno,
  ROUND(MAX(sal)) AS Maximum,
  ROUND(MIN(sal)) AS Minimum,
  ROUND(SUM(sal)) AS Total,
  ROUND(AVG(sal)) AS Average
FROM emp
GROUP BY deptno;

🔹 2. Display Department no and number of managers working in that department

SELECT 
  deptno,
  COUNT(*) AS 'Total Number of Managers'
FROM emp
WHERE job = 'MANAGER'
GROUP BY deptno;

🔹 3. Department number and sum of salary of all non-managers where sum > 20000

SELECT 
  deptno,
  SUM(sal) AS Total_Salary
FROM emp
WHERE job <> 'MANAGER'
GROUP BY deptno
HAVING SUM(sal) > 20000;

🔹 4. Display department name, sum of salary, and employee count department-wise
Assuming you have a dept table with dname (department name) and deptno:

SELECT 
  d.dname AS Department_Name,
  SUM(e.sal) AS Total_Salary,
  COUNT(e.empno) AS Employee_Count
FROM emp e
JOIN dept d ON e.deptno = d.deptno
GROUP BY d.dname;


# 🛒 PRODUCT TABLE TASKS

🔹 1. Create Product Table and Insert Rows

CREATE TABLE product (
  pid INT,
  pname VARCHAR(50),
  qty INT,
  amount DECIMAL(10,2),
  company VARCHAR(50)
);

-- Example inserts
INSERT INTO product VALUES 
(101, 'Mouse', 20, 300, 'Logitech'),
(102, 'Keyboard', 10, 700, 'Logitech'),
(103, 'Monitor', 5, 8000, 'Dell'),
(104, 'Laptop', 2, 50000, 'HP'),
(105, 'USB Cable', 50, 150, 'Boat'),
(106, 'Speaker', 10, 1200, 'Boat'),
(107, 'Printer', 1, 7000, 'Canon');

🔹 a. Display total amount and total quantity sold for each company

SELECT 
  company,
  SUM(amount) AS Total_Amount,
  SUM(qty) AS Total_Quantity
FROM product
GROUP BY company;

🔹 b. Display how many products are there for each company

SELECT 
  company,
  COUNT(*) AS Product_Count
FROM product
GROUP BY company;

🔹 c. Display pname, company, and total amount for each company

SELECT 
  company,
  pname,
  SUM(amount) AS Total_Amount
FROM product
GROUP BY company, pname;

🔹 d. Display average amount for each product

SELECT 
  pname,
  AVG(amount) AS Average_Amount
FROM product
GROUP BY pname;

🔹 e. Display company names, product name, and average amount product-wise

SELECT 
  company,
  pname,
  AVG(amount) AS Avg_Amount
FROM product
GROUP BY company, pname;
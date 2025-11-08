CREATE TABLE dept (
deptno decimal(2,0) primary key,
dname varchar(14) default NULL,
loc varchar(13) default NULL
);

CREATE TABLE emp (
empno decimal(4,0) primary key,
ename varchar(10) default NULL,
mgr_no decimal(4,0) default NULL,
hiredate date default NULL,
sal decimal(7,2) default NULL,
deptno decimal(2,0) references dept(deptno) on delete cascade on update cascade
);

create table incentives(empno decimal(4,0) references emp(empno) on delete cascade on update cascade,incentive_date date,incentive_amount decimal(10,2), primary key(empno,incentive_date));

Create table project (
pno int primary key,
pname varchar(30) not null,
ploc varchar(30)
);

Create table assigned_to (
empno decimal(4,0) references emp(empno) on delete cascade on update cascade,
pno int references project(pno) on delete cascade on update cascade,
job_role varchar(30),
primary key(empno,pno)
);

INSERT INTO dept VALUES (10,'ACCOUNTING','MUMBAI');
INSERT INTO dept VALUES (20,'RESEARCH','BENGALURU');
INSERT INTO dept VALUES (30,'SALES','DELHI');
INSERT INTO dept VALUES (40,'OPERATIONS','CHENNAI');

INSERT INTO emp VALUES(7369,'Adarsh',7902,'2012-12-17','80000.00','20');
insert into emp values(7499,'Shruthi',7698,'2013-02-20','16000.00','30');
insert into emp values (7521,'Anvitha',7698,'2015-02-22','12500.00','30');
insert into emp values(7566,'Tanvir',7839,'2008-04-02','29750.00','20');
insert into emp values(7654,'Ramesh','7698','2014-09-28','12500.00','30');

insert into incentives values(7499,'2019-02-01',5000.00);
insert into incentives values(7521,'2019-03-01',2500.00);
insert into incentives values(7566,'2022-02-01',5070.00);
insert into incentives values(7654,'2020-02-01',2000.00);
insert into incentives values(7654,'2022-04-01',879.00);

insert into project values(101,'ai project','bengaluru');
insert into project values(102,'iot','hyderabad');
insert into project values(103,'blockchain','bengaluru');
insert into project values(104,'data science','mysuru');
insert into project values(105,'autonomous systems','pune');

select * from dept;
select * from emp;
select * from incentives;
select * from project;

select empno from emp where not exists(select * from incentives i,emp e where  e.empno=i.empno);
select m.ename,count(*) from emp e,emp m where e.mgr_no=m.empno group by m.ename having count(*) = (select max(mycount) from (select count(*) mycount from emp group by mgr_no)a);

select * from emp m where m.empno in (select mgr_no from emp) and m.sal>(select avg(e.sal) from emp e where e.mgr_no=m.empno); 

select distinct m.mgr_no from emp e ,emp m where e.mgr_no=m.mgr_no and e.deptno=m.deptno  and  e.empno in(select distinct m.mgr_no from emp e,emp m where e.mgr_no=m.mgr_no and e.deptno=m.deptno);
 
select * from emp e,incentives i where e.empno=i.empno and 2=(select count(*) from incentives j where i.incentive_amount <= j.incentive_amount);
 
select * from emp e where e.deptno=(select e1.deptno from emp e1 where e1.empno=e.mgr_no);
 
select e.empno
from emp e, assigned_to a, project p
where e.empno=a.empno and a.pno=p.pno and
p.ploc in ('Bengaluru','Hyderabad','Mysuru');

select e.empno,e.ename,d.dname,a.job_role,p.ploc from emp e,dept d , assigned_to a,project p where e.deptno=d.deptno and e.empno=a.empno and a.pno=p.pno and d.loc=p.ploc;

select distinct e.ename from emp e,incentives i where(select max(sal+incentive_amount) from emp,incentives)>=any(select sal from emp e1 where e.deptno=e1.deptno);

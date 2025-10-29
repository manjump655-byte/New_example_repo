# New_example_repo
my second repository with love
-- In Snowflake we can create constraints but only NOT NULL constraint will work. other constraints wont work
--CONSTRAINTS: Conditions/restrictions 
--SQL constraints are used to specify rules for data in a table.
1)NOT NULL -- Ensures that a column cannot have a NULL value

CREATE TABLE DIMCUST(CUSTID INT,CUSTNAME VARCHAR(50),CUSTBAL INT)
INSERT INTO DIMCUST VALUES(10,'RAJESH', 50000)
INSERT INTO DIMCUST(CUSTNAME,CUSTBAL) VALUES('RAJESH', 50000)

select * from DIMCUST
DROP TABLE DIMCUST

CREATE or REPLACE TABLE DIMCUST(CUSTID INT NOT NULL,CUSTNAME VARCHAR(50),CUSTBAL INT)
INSERT INTO DIMCUST(CUSTNAME,CUSTBAL) VALUES('RAJESH', 50000)
INSERT INTO DIMCUST(CUSTID,CUSTNAME,CUSTBAL) VALUES(1,'RAJESH', 50000)
INSERT INTO DIMCUST(CUSTID,CUSTNAME,CUSTBAL) VALUES(null,'Hari', 50000)

/*error
Msg 515, Level 16, State 2, Line 1
Cannot insert the value NULL into column 'CUSTID', table 'hitesh.dbo.DIMCUST'; column does not allow nulls. INSERT fails.
The statement has been terminated.*/
alter TABLE DIMCUST ALTER COLUMN CUSTBAL INT NOT NULL

INSERT INTO DIMCUST(CUSTID,CUSTNAME,CUSTBAL) VALUES(3,'Kiran', null)

SELECT * FROM DIMCUST

2) UNIQUE -- Ensures that all values in a column are different
ALTER TABLE DIMCUST ADD CUSTID INT UNIQUE 
-- add unique constraint to existing column+
ALTER TABLE DIMCUST ADD CONSTRAINT UX_1 UNIQUE(CUSTID)

TRUNCATE TABLE DIMCUST

--DESCRIBE TABLE DIMCUST

INSERT INTO DIMCUST(CUSTID,CUSTNAME,CUSTBAL) VALUES(1,'RAJESH', 50000)

INSERT INTO DIMCUST(CUSTID,CUSTNAME,CUSTBAL,CUSTNO) VALUES(2,'Raj', 7000,978) --Violation of UNIQUE KEY constraint 'UQ__DIMCUST__3D9DE38B99FEEC85'. Cannot insert duplicate key in object 'dbo.DIMCUST'. The duplicate key value is (978).

INSERT INTO DIMCUST(CUSTID,CUSTNAME,CUSTBAL,CUSTNO) VALUES(2,'Raj', 7000,979)

INSERT INTO DIMCUST(CUSTID,CUSTNAME,CUSTBAL,CUSTNO) VALUES(3,'jazir', 8000,null)

INSERT INTO DIMCUST(CUSTID,CUSTNAME,CUSTBAL,CUSTNO) VALUES(4,'Ravi', 9000,null)
select * from DIMCUST


3) Default -- Sets a default value for a column if no value is specified
create table custdtl(custid int unique, custname varchar(50),
custbranch varchar(50) default 'bangalore')


alter table custdtl add constraint df_123 default 'dummy' for custname

/*alter table t1 alter column c1 drop not null;

alter table t1 modify c2 drop default, c3 set default 'text' ;

alter table t1 alter c4 set data type varchar(50), column c4 drop default;

alter table t1 alter c5 comment '50 character column'; */

insert into custdtl(custid) values (667)
insert into custdtl (custid,custname) values (56,'hari')
select * from custdtl
insert into custdtl (custid,custname) values (100,'kumar')
insert into custdtl values(1,'seenu', 'hyd')
select * from custdtl
insert into custdtl
select 7, 'venki','chennai' union all
select 5, 'raji','mum' union all
select 22, 'muni','pune'
select * from emp_dtl
4) check constraint: Ensures that the values in a column satisfies a specific condition
alter table custdtl add custbal int
alter table custdtl add constraint ck_1 check(custbal>5000)

select * from custdtl

insert into custdtl values(3,'ram', 'hyd', 3000)  -- throws error since we are are entering lesser value for custbal
insert into custdtl values(3,'ram', 'hyd', 5001)
create table custdtl2(custid int, custname varchar(50), custbal int check(custbal>10000))

5) Primary Key: combination of unique & not null
create table pk_test(custid int primary key, custname varchar(50), custbal int)
insert into pk_test(custname,custbal) values('hitesh',565876) --null

select * from pk_test where custid=1 

insert into pk_test(custid,custname,custbal) values(1,'hitesh',565876)
insert into pk_test(custid,custname,custbal) values(2,'kiran',7000)
select * from pk_test

/*error-
Msg 515, Level 16, State 2, Line 1
Cannot insert the value NULL into column 'custid', table 'hitesh.dbo.pk_test'; column does not allow nulls. INSERT fails.
The statement has been terminated.*/
insert into pk_test values(1,'hitesh',565876)
insert into pk_test values(1,'kartik',68778)---duplicate
alter table pk_test alter column custbal int not null

alter table pk_test add constraint pk_56 primary key(custbal) ----only one primary key per table
alter table custdtl alter column custbal int not null   
----can add primary key only if it is nullable column n doesnot contain any dupi=licate
alter table custdtl add constraint pk_56 primary key(custbal)


create table parent (id int primary key, name varchar(50), empsal int)
--Foreign Key : Prevents actions that would destroy links between tables
-- Primary and Foreign references can be created in snowflake but cant be enforced

create table child (id int foreign key references parent(id), branch varchar(50))
insert into child values(1,'hyd') 
----if a value trying to insert into foreign key column in child/reference that value should be in parent/base table
insert into parent values(1,'ravi', 26878) -- while inserting data you will have insert into parent first and then insert into child
insert into child values(1,'hyd')

insert into child values(2,'blr')


select * from parent
delete from parent where id=1
delete from child where id=1  -- while deleting first delete that refernce record/row from child then you can delete from parenr
delete from parent where id=1

select * from child

create table customer(id int, name varchar(50),custbal int);
insert into customer
select null,'Ram',50000 

union all
select 2,'krishna',150000 union all
select 4,'ravi',90000 union all
select 5,'seema', 200000 union all
select 6,'Rajesh', 170000 union all
select 7,'Madhu', 70000

create table branch(id int, branch varchar(50),empdesg varchar(50))
select * from customer
select * from branch

insert into branch values(
null,'Patna','TL')

select 1,'pune','SSE' 

union all
select 2,'Hyderabad','MG' union all
select 4,'Chennai','TL' union all
select 5,'Kolkatta',' SM' union all
select 8,'Mumbai','AM' union all
select 9,'Mumbai','SE'


/*INNER JOIN: Returns records that have matching values in both tables
LEFT OUTER JOIN/ LEFT JOIN: Returns all records from the left table, and the matched records from the right table
RIGHT OUTER JOIN/RIGHT: Returns all records from the right table, and the matched records from the left table
FULL OUTER JOIN: Returns all records when there is a match in either left or right table 
CROSS JOIN:  cartesian product left table has got m records and right table has got n records it will generate mxn as output
*/

select * from customer
select * from branch
select a.id,a.name,a.custbal, b.branch, b.empdesg from customer a inner join 
branch b on a.id=b.id

select a.id,a.name,a.custbal, b.branch, b.empdesg from customer a left join
branch b on a.id=b.id where b.id is null
select a.id,a.name,a.custbal, b.branch, b.empdesg,b.id as right_id  
from customer a right join branch b on a.id=b.id where a.id is null

m n
m*n = cartesian product

select a.id,a.name,a.custbal, b.branch, b.empdesg,b.id as right_id 
from customer a full outer join branch b on a.id=b.id where a.id is null or 
b.id is null

select a.id,a.name,a.custbal, b.branch, b.empdesg from 
customer a cross join branch b

-- if there are duplicates for key fields that were used in joins
insert into customer
select 1,'Kiran',50000 union all
select 1,'Ramesh',50000

insert into branch
select 1,'Hyderabad','SSE'

select a.id,a.name,a.custbal, b.branch, b.empdesg from customer a inner join branch b on a.id=b.id
select a.id,a.name,a.custbal, b.branch, b.empdesg from customer a left join branch b on a.id=b.id
select b.id,a.name,a.custbal, b.branch, b.empdesg from customer a right join branch b on a.id=b.id
select a.id,a.name,a.custbal, b.branch, b.empdesg,b.id as right_id from customer a full outer join branch b on a.id=b.id
select a.id,a.name,a.custbal, b.branch, b.empdesg from customer a cross join branch b

-- insert null values into key fields
insert into customer
select null,'Anil',50000

insert into branch
select null,'Pune','SSE'

select * from customer where id is null
select * from branch
select * from customer where id is null

select a.id,a.name,a.custbal, b.branch, b.empdesg from customer a inner join branch b on a.id=b.id
select a.id,a.name,a.custbal, b.branch, b.empdesg from customer a left join branch b on a.id=b.id
select a.id,a.name,a.custbal, b.branch, b.empdesg,b.id as right_id  from customer a right join branch b on a.id=b.id
select a.id,a.name,a.custbal, b.branch, b.empdesg,b.id as right_id from customer a full outer join branch b on a.id=b.id
select a.id,a.name,a.custbal, b.branch, b.empdesg from customer a cross join branch b

--Non matching records from left table
select a.id,a.name,a.custbal, b.branch, b.empdesg,b.id from customer a left join branch b on a.id=b.id where b.id is null
--Non matching records from right table
select a.id,a.name,a.custbal, b.branch, b.empdesg,b.id from customer a right join branch b on a.id=b.id where a.id is null

hihj
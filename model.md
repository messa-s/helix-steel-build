1. Create a following Library Management system and insert values into it Member(Mem_no,mem_name,no of books,fine) 
Book(book_no,book_name,author,price,no of books) 
Transaction(book_no,mem_no,date of issue,date of return)
 List all books borrowed by Gokul
 List count of books borrowed by Gokul on date 
Calculate the total fine amount of books borrowed by the member Gokul
 
2. Write a PL/SQL block to issue books to a member by accepting member name and book number

CREATE TABLE Member (
    mem_no NUMBER PRIMARY KEY,
    mem_name VARCHAR2(50),
    no_of_books NUMBER DEFAULT 0,
    fine NUMBER(10,2) DEFAULT 0
);

CREATE TABLE Book (
    book_no NUMBER PRIMARY KEY,
    book_name VARCHAR2(100),
    author VARCHAR2(50),
    price NUMBER(10,2),
    no_of_books NUMBER DEFAULT 1
);

CREATE TABLE Transaction (
    book_no NUMBER,
    mem_no NUMBER,
    date_of_issue DATE,
    date_of_return DATE,
    PRIMARY KEY (book_no, mem_no, date_of_issue),
    FOREIGN KEY (book_no) REFERENCES Book(book_no),
    FOREIGN KEY (mem_no) REFERENCES Member(mem_no)
);

INSERT INTO Member VALUES (1, 'Gokul', 2, 20);
INSERT INTO Member VALUES (2, 'Arun', 1, 10);
INSERT INTO Member VALUES (3, 'Rahul', 0, 0);
INSERT INTO Member VALUES (4, 'Anu', 1, 5);

INSERT INTO Book VALUES (101, 'DBMS', 'Korth', 500, 5);
INSERT INTO Book VALUES (102, 'Oracle SQL', 'Ivan Bayross', 450, 3);
INSERT INTO Book VALUES (103, 'Java', 'Herbert Schildt', 600, 4);
INSERT INTO Book VALUES (104, 'Python', 'Mark Lutz', 550, 2);
INSERT INTO Book VALUES (105, 'C Programming', 'Dennis Ritchie', 400, 6);

INSERT INTO Transaction
VALUES (101, 1, DATE '2026-09-01', DATE '2026-09-10');

INSERT INTO Transaction
VALUES (102, 1, DATE '2026-09-05', NULL);

INSERT INTO Transaction
VALUES (103, 2, DATE '2026-09-03', NULL);

INSERT INTO Transaction
VALUES (104, 4, DATE '2026-09-02', NULL);

COMMIT;

i)
SELECT b.book_no, b.book_name, b.author
FROM Book b
JOIN Transaction t
ON b.book_no = t.book_no
JOIN Member m
ON t.mem_no = m.mem_no
WHERE m.mem_name = 'Gokul';

ii)
select count(*) as no_of_books
from Transaction t
join Member m
on t.MEM_NO = m.MEM_NO
where m.mem_name = 'Gokul'
and t.date_of_issue = DATE '2026-09-05';

select t.date_of_issue, count(*) as no_of_books
from Transaction t
join MEMBER m
on t.mem_no = m.mem_no
where m.mem_no = m.mem_no
group by t.date_of_issue
order by t.date_of_issue;




iii)
select mem_name, sum(fine) as total_fine
from member
where mem_name = 'Gokul'
group by mem_name;

SET SERVEROUTPUT ON;


DECLARE
   v_mem_name Member.mem_name%TYPE := '&member_name';
   v_book_no Book.book_no%TYPE := &book_number;


   v_mem_no Member.mem_no%TYPE;
   v_available Book.no_of_books%TYPE;
BEGIN


   SELECT mem_no
   INTO v_mem_no
   FROM Member
   WHERE UPPER(mem_name) = UPPER(v_mem_name);


   SELECT no_of_books
   INTO v_available
   FROM Book
   WHERE book_no = v_book_no;


   IF v_available > 0 THEN


       INSERT INTO Transaction
       VALUES (
           v_book_no,
           v_mem_no,
           SYSDATE,
           NULL
       );


       UPDATE Book
       SET no_of_books = no_of_books - 1
       WHERE book_no = v_book_no;


       UPDATE Member
       SET no_of_books = no_of_books + 1
       WHERE mem_no = v_mem_no;


       COMMIT;


       DBMS_OUTPUT.PUT_LINE('Book issued successfully');


   ELSE


       DBMS_OUTPUT.PUT_LINE('Book is not available');


   END IF;


EXCEPTION


   WHEN NO_DATA_FOUND THEN
       DBMS_OUTPUT.PUT_LINE('Member or Book not found');


   WHEN DUP_VAL_ON_INDEX THEN
       DBMS_OUTPUT.PUT_LINE('Book is already issued to this member');


   WHEN OTHERS THEN
       DBMS_OUTPUT.PUT_LINE('Error: ' || SQLERRM);


END;
/


JOIN
INNER Join
It returns rows where there is a matching value in both tables.
SELECT columns
FROM table1
JOIN table2
ON table1.column = table2.column;

3 Consider the following schema:
 Sailors(Sid,Sname,rating,age) 
Boats(bid,bname,color) 
Reserves(Sid,bid,day(date))
 Write query statement for the following queries.
 1. Find the name of sailors who have reserved both red and yellow boat 
2. Find all sids of sailors who have a rating of 10 or have reserved boat 111 
3. Find the name of sailors who have reserved boat 103 
4 Write a PL/SQL program to retrieve the details of boat by providing the colour using cursor

CREATE TABLE Sailors (
   Sid NUMBER PRIMARY KEY,
   Sname VARCHAR2(30),
   rating NUMBER,
   age NUMBER
);


CREATE TABLE Boats (
   bid NUMBER PRIMARY KEY,
   bname VARCHAR2(30),
   color VARCHAR2(20)
);


CREATE TABLE Reserves (
   Sid NUMBER,
   bid NUMBER,
   day DATE,
   PRIMARY KEY (Sid, bid, day),
   FOREIGN KEY (Sid) REFERENCES Sailors(Sid),
   FOREIGN KEY (bid) REFERENCES Boats(bid)
);

INSERT INTO Sailors VALUES (1, 'Gokul', 10, 25);
INSERT INTO Sailors VALUES (2, 'Arun', 8, 30);
INSERT INTO Sailors VALUES (3, 'Rahul', 7, 22);
INSERT INTO Sailors VALUES (4, 'Akhil', 10, 28);
INSERT INTO Sailors VALUES (5, 'Vishnu', 6, 35);
INSERT INTO Boats VALUES (101, 'Sea King', 'red');
INSERT INTO Boats VALUES (102, 'Ocean Star', 'yellow');
INSERT INTO Boats VALUES (103, 'Blue Wave', 'blue');
INSERT INTO Boats VALUES (104, 'Red Dragon', 'red');
INSERT INTO Boats VALUES (105, 'Yellow Bird', 'yellow');
INSERT INTO Boats VALUES (111, 'Speed Boat', 'green');
INSERT INTO Reserves VALUES (1, 101, DATE '2026-09-01');
INSERT INTO Reserves VALUES (1, 102, DATE '2026-09-02');


INSERT INTO Reserves VALUES (2, 103, DATE '2026-09-03');


INSERT INTO Reserves VALUES (3, 104, DATE '2026-09-04');
INSERT INTO Reserves VALUES (3, 105, DATE '2026-09-05');


INSERT INTO Reserves VALUES (4, 111, DATE '2026-09-06');


INSERT INTO Reserves VALUES (5, 103, DATE '2026-09-07');



i)
SELECT DISTINCT s.Sname
FROM Sailors s
JOIN Reserves r ON s.Sid = r.Sid
JOIN Boats b ON r.bid = b.bid
WHERE b.color = 'red'
AND s.Sid IN (
   SELECT r2.Sid
   FROM Reserves r2
   JOIN Boats b2 ON r2.bid = b2.bid
   WHERE b2.color = 'yellow'
);

ii)
SELECT Sid
FROM Sailors
WHERE rating = 10


UNION


SELECT Sid
FROM Reserves
WHERE bid = 111;

iii)
SELECT DISTINCT s.Sname
FROM Sailors s
JOIN Reserves r ON s.Sid = r.Sid
WHERE r.bid = 103;

SET SERVEROUTPUT ON;

DECLARE
    v_color Boats.color%TYPE := '&Enter_Color';

    CURSOR boat_cursor IS
        SELECT bid, bname, color
        FROM Boats
        WHERE LOWER(color) = LOWER(v_color);

    v_bid Boats.bid%TYPE;
    v_bname Boats.bname%TYPE;
    v_boat_color Boats.color%TYPE;

BEGIN
    OPEN boat_cursor;

    LOOP
        FETCH boat_cursor INTO v_bid, v_bname, v_boat_color;

        EXIT WHEN boat_cursor%NOTFOUND;

        DBMS_OUTPUT.PUT_LINE('Boat ID: ' || v_bid);
        DBMS_OUTPUT.PUT_LINE('Boat Name: ' || v_bname);
        DBMS_OUTPUT.PUT_LINE('Color: ' || v_boat_color);
        DBMS_OUTPUT.PUT_LINE('--------------------');
    END LOOP;

    CLOSE boat_cursor;
END;
/



Below is a simple Oracle SQL + PL/SQL solution suitable for a lab/exam.
5. Employee Database
1. Create tables
CREATE TABLE Employee (
    Empid NUMBER PRIMARY KEY,
    Deptid NUMBER,
    role VARCHAR2(30),
    salary NUMBER
);

CREATE TABLE Edetails (
    Empid NUMBER PRIMARY KEY,
    Name VARCHAR2(50),
    Location VARCHAR2(50),
    Date_of_joining DATE,
    FOREIGN KEY (Empid) REFERENCES Employee(Empid)
);

CREATE TABLE Department (
    Deptid NUMBER PRIMARY KEY,
    deptname VARCHAR2(50),
    Location VARCHAR2(50)
);

Insert sample values
INSERT INTO Employee VALUES (101, 40, 'Team Leader', 60000);
INSERT INTO Employee VALUES (102, 40, 'Software Architect', 80000);
INSERT INTO Employee VALUES (103, 20, 'Developer', 45000);
INSERT INTO Employee VALUES (104, 40, 'Developer', 50000);
INSERT INTO Employee VALUES (105, 30, 'Tester', 40000);
INSERT INTO Employee VALUES (106, 20, 'Team Leader', 65000);

INSERT INTO Edetails VALUES (101, 'Arun', 'Kollam', DATE '2022-05-10');
INSERT INTO Edetails VALUES (102, 'Rahul', 'Kochi', DATE '2020-03-15');
INSERT INTO Edetails VALUES (103, 'Gokul', 'Kollam', DATE '2025-06-20');
INSERT INTO Edetails VALUES (104, 'Amal', 'Trivandrum', DATE '2023-08-01');
INSERT INTO Edetails VALUES (105, 'Vishnu', 'Kochi', DATE '2025-01-10');
INSERT INTO Edetails VALUES (106, 'Anu', 'Kollam', DATE '2021-11-25');

INSERT INTO Department VALUES (40, 'Software', 'Kochi');
INSERT INTO Department VALUES (20, 'Development', 'Trivandrum');
INSERT INTO Department VALUES (30, 'Testing', 'Kochi');

COMMIT;


1. Employees in department 40 who are Team Leader or Software Architect
We need Edetails for the name, and Employee for Deptid and role.
SELECT e.Empid, d.Name, e.Deptid, e.role
FROM Employee e
JOIN Edetails d
ON e.Empid = d.Empid
WHERE e.Deptid = 40
AND e.role IN ('Team Leader', 'Software Architect');

Output would be approximately:
EMPID   NAME    DEPTID   ROLE
101     Arun      40     Team Leader
102     Rahul     40     Software Architect

IN is useful when you want to compare a column with multiple possible values.
This:
e.role IN ('Team Leader', 'Software Architect')

is equivalent to:
e.role = 'Team Leader'
OR e.role = 'Software Architect'


2. Count employees belonging to each role
SELECT role, COUNT(*) AS employee_count
FROM Employee
GROUP BY role;

Example output:
ROLE                  EMPLOYEE_COUNT
Team Leader                 2
Software Architect          1
Developer                   2
Tester                      1

The important concept here is:
GROUP BY role

It groups employees having the same role, and:
COUNT(*)

counts the employees in each group.

3. Employees with more than one year of experience
Use MONTHS_BETWEEN() to calculate the difference between today's date and joining date.
SELECT e.Empid, d.Name, d.Location,
       d.Date_of_joining, e.Deptid, e.role, e.salary
FROM Employee e
JOIN Edetails d
ON e.Empid = d.Empid
WHERE MONTHS_BETWEEN(SYSDATE, d.Date_of_joining) > 12;

Simpler version
You can also use:
SELECT e.Empid, d.Name, d.Location,
       d.Date_of_joining, e.Deptid, e.role, e.salary
FROM Employee e
JOIN Edetails d
ON e.Empid = d.Empid
WHERE d.Date_of_joining < ADD_MONTHS(SYSDATE, -12);

The second version is particularly easy to understand:
ADD_MONTHS(SYSDATE, -12)

means the date exactly one year ago.
So:
Date_of_joining < ADD_MONTHS(SYSDATE, -12)

means the employee joined more than one year ago.

6. PL/SQL Cursor
Question: Write a PL/SQL block which accepts employee ID from the user and displays the name, location and role using a cursor.
SET SERVEROUTPUT ON;

DECLARE
    v_empid Employee.Empid%TYPE;

    CURSOR emp_cursor IS
        SELECT d.Name, d.Location, e.role
        FROM Employee e
        JOIN Edetails d
        ON e.Empid = d.Empid
        WHERE e.Empid = v_empid;

    v_name Edetails.Name%TYPE;
    v_location Edetails.Location%TYPE;
    v_role Employee.role%TYPE;

BEGIN
    v_empid := &empid;

    OPEN emp_cursor;

    FETCH emp_cursor INTO v_name, v_location, v_role;

    IF emp_cursor%FOUND THEN
        DBMS_OUTPUT.PUT_LINE('Name     : ' || v_name);
        DBMS_OUTPUT.PUT_LINE('Location : ' || v_location);
        DBMS_OUTPUT.PUT_LINE('Role     : ' || v_role);
    ELSE
        DBMS_OUTPUT.PUT_LINE('Employee not found');
    END IF;

    CLOSE emp_cursor;
END;
/

When you run it, Oracle SQL*Plus/SQL Developer will ask:
Enter value for empid:

For example:
Enter value for empid: 101

Output:
Name     : Arun
Location : Kollam
Role     : Team Leader

How the cursor works
The important parts are:
CURSOR emp_cursor IS
    SELECT ...

Creates the cursor.
OPEN emp_cursor;

Opens the cursor and executes the query.
FETCH emp_cursor INTO v_name, v_location, v_role;

Gets the result from the cursor.
IF emp_cursor%FOUND THEN

Checks whether a record was found.
CLOSE emp_cursor;

Closes the cursor.
Exam shortcut to remember
For a basic explicit cursor, remember:
DECLARE
    CURSOR cursor_name IS SELECT ...;
BEGIN
    OPEN cursor_name;
    FETCH cursor_name INTO variables;
    CLOSE cursor_name;
END;
/

And for this question, the overall concepts are JOIN + IN + GROUP BY + COUNT + date functions + PL/SQL cursor.


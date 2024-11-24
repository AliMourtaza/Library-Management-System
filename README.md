# Library-Management-System

This project demonstrates the implementation of a Library Management System using SQL. It includes creating and managing tables, performing CRUD operations, and executing advanced SQL queries. The goal is to showcase database design, manipulation, and querying skills.

## Objectives

1. **Set up the Library Management System Database**: Create and populate the database with tables for branches, employees, members, books, issued status, and return status.
2. **CRUD Operations**: Perform Create, Read, Update, and Delete operations on the data.
3. **CTAS (Create Table As Select)**: Utilize CTAS to create new tables based on query results.
4. **Advanced SQL Queries**: Develop complex queries to analyze and retrieve specific data.

## Project Structure

### 1. Database Setup
![ERD](https://github.com/najirh/Library-System-Management---P2/blob/main/library_erd.png)

- **Database Creation**: Created a database named `Library Management System `.
- **Table Creation**: Created tables for branches, employees, members, books, issued status, and return status. Each table includes relevant columns and relationships.

```
--Create Branch Table

CREATE TABLE branch
(
	branch_id VARCHAR (10) PRIMARY KEY,
	manager_id VARCHAR (10),
	branch_address VARCHAR (55),
	contact_no VARCHAR (10)
);

ALTER TABLE branch
ALTER COLUMN contact_no TYPE VARCHAR (15);

--Create Branch Table

CREATE TABLE employees
(
	emp_id VARCHAR (10) PRIMARY KEY,
	emp_name VARCHAR (25),
	position VARCHAR (15),
	salary INT,
	branch_id VARCHAR (10) --FK
);

ALTER TABLE employees
ALTER COLUMN salary TYPE FLOAT;

--Create Books Table

CREATE TABLE books
(
	isbn VARCHAR (20) PRIMARY KEY,
	book_title VARCHAR (75),
	category VARCHAR (10),
	rental_price FLOAT,
	status VARCHAR (15),
	author VARCHAR (35),
	publisher VARCHAR (55)
);

ALTER TABLE books
ALTER COLUMN category TYPE VARCHAR (20);

--Create Members Table

CREATE TABLE members
(
	member_id VARCHAR (10) PRIMARY KEY,
	member_name VARCHAR (25),
	member_address VARCHAR (75),
	reg_date DATE
);

--Create Issued_status Table

CREATE TABLE issued_status
(
	issued_id VARCHAR (10) PRIMARY KEY,
	issued_member_id VARCHAR (10), --FK
	issued_book_name VARCHAR (75),
	issued_date DATE,
	issued_book_isbn VARCHAR (25), --FK
	issued_emp_id VARCHAR (10) --FK
);

--Create Return_status Table

CREATE TABLE return_status
(
	return_id VARCHAR (10) PRIMARY KEY,
	issued_id VARCHAR (10), --FK
	return_book_name VARCHAR (75),
	return_date DATE,
	return_book_isbn VARCHAR (20)
);

---FOREIGN KEY
ALTER TABLE issued_status
ADD CONSTRAINT fk_member
FOREIGN KEY (issued_member_id)
REFERENCES members(member_id);

ALTER TABLE issued_status
ADD CONSTRAINT fk_books
FOREIGN KEY (issued_book_isbn)
REFERENCES books(isbn);

ALTER TABLE issued_status
ADD CONSTRAINT fk_employees
FOREIGN KEY (issued_emp_id)
REFERENCES employees(emp_id);

ALTER TABLE employees
ADD CONSTRAINT fk_branch
FOREIGN KEY (branch_id)
REFERENCES branch(branch_id);

ALTER TABLE return_status
ADD CONSTRAINT fk_issued_status
FOREIGN KEY (issued_id)
REFERENCES issued_status(issued_id);
```

### 2. CRUD Operations

- **Create**: Inserted sample records into the `books` table.
- **Read**: Retrieved and displayed data from various tables.
- **Update**: Updated records in the `employees` table.
- **Delete**: Removed records from the `members` table as needed.

**Task 1. Create a New Book Record**
-- "978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')"
```
INSERT INTO books
		(isbn, book_title, category, rental_price, status, author, publisher)
VALUES
		('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.');
```

**Task 2: Update Member's Address '125 Oak St' of member_id 'C103**

```
UPDATE members
SET member_address = '125 Oak St'
WHERE member_id = 'C103';
```

**Task 3: Delete the record with issued_id = 'IS121' from the issued_status table**

```
DELETE FROM issued_status
WHERE issued_id = 'IS121';
```

**Task 4: Select all books issued by the employee with emp_id = 'E101'**

```
SELECT *
FROM issued_status
WHERE issued_emp_id = 'E101';
```

**Task 5: List Employees Who Have Issued More Than One Book**

```
SELECT 
	issued_emp_id,
	COUNT(*) AS total_issued
FROM issued_status
GROUP BY 1
HAVING COUNT(*) > 1;
```

### 3. CTAS (Create Table As Select)

**Task 6: Create Summary Tables**: Used CTAS to generate new tables based on query results - each book and total book_issued_cnt**

```
CREATE TABLE book_issued_cnt AS 
SELECT
		b.isbn,
		b.book_title,
		COUNT(ist.issued_id) AS issued_count
FROM issued_status AS ist
JOIN
books AS b
ON ist.issued_book_isbn  = b.isbn
GROUP BY 1,2;
```

### 4. Data Analysis & Findings

The following SQL queries were used to address specific questions:

Task 7. **Retrieve All Books in a Specific Category**:

```
SELECT *
FROM books
WHERE category = 'History';
```

Task 8: **Find Total Rental Income by Category**

```
SELECT
	b.category,
	SUM(b.rental_price) as rental_income,
	COUNT(ist.issued_id)
FROM books AS b
JOIN
issued_status AS ist
ON 
b.isbn = ist.issued_book_isbn
GROUP BY 1
```

Task 9: **List Members Who Registered in the Last 180 Days**:

```
SELECT *
FROM members
WHERE reg_date > CURRENT_DATE - INTERVAL '180 days';
```

10. **List Employees with Their Branch Manager's Name and their branch details**:

```
SELECT
	e1.emp_id,
    e1.emp_name,
    e1.position,
    e1.salary,
	b.*,
	e2.emp_name AS manager
FROM employees AS e1
JOIN
branch AS b
ON
e1.branch_id= b.branch_id
JOIN
employees AS e2
ON
e2.emp_id = b.manager_id
```

Task 11. **Create a Table of Books with Rental Price Above a Certain Threshold**:

```
CREATE TABLE expensive_books AS
SELECT *
FROM books
WHERE rental_price > (SELECT
			AVG(rental_price)
			FROM books)
```

Task 12: **Retrieve the List of Books Not Yet Returned**

```
SELECT DISTINCT issued_book_name
FROM issued_status AS ist
LEFT JOIN
return_status AS rs
ON 
ist.issued_id = rs.issued_id
WHERE rs.return_id IS NULL
```

## Advanced SQL Operations

Task 13: **Identify Members with Overdue Books** 
Write a query to identify members with overdue books (assuming a 30-day return period). Display the member's_id, member's name, book title, issue date, and days overdue.

```
SELECT
	ist.issued_member_id,
	m.member_name,
	b.book_title,
	ist.issued_date,
	CURRENT_DATE - ist.issued_date AS overdue
FROM issued_status AS ist
JOIN
members AS m
ON ist.issued_member_id = m.member_id
JOIN
books AS b
ON ist.issued_book_isbn = b.isbn
LEFT JOIN 
return_status AS rs
ON ist.issued_id = rs.issued_id
WHERE
	return_date IS NULL
	AND CURRENT_DATE - ist.issued_date > 30
```

Task 14: **Update Book Status on Return**  
Write a query to update the status of books in the books table to "Yes" when they are returned (based on entries in the return_status table).

```
CREATE OR REPLACE PROCEDURE add_return_records(p_return_id VARCHAR(10), p_issued_id VARCHAR(10))
LANGUAGE plpgsql
AS $$

DECLARE
	v_isbn VARCHAR(50);
    v_book_name VARCHAR(80);

BEGIN
	
	-- inserting into returns based on users input
    INSERT INTO return_status(return_id, issued_id, return_date)
    VALUES
    (p_return_id, p_issued_id, CURRENT_DATE);

    SELECT 
        issued_book_isbn,
        issued_book_name
        INTO
        v_isbn,
        v_book_name
    FROM issued_status
    WHERE issued_id = p_issued_id;

    UPDATE books
    SET status = 'yes'
    WHERE isbn = v_isbn;

    RAISE NOTICE 'Thank you for returning the book: %', v_book_name;

END;
$$

-- calling function 
CALL add_return_records('RS138', 'IS135');

-- calling function 
CALL add_return_records('RS148', 'IS140');

SELECT * FROM return_status
SELECT * FROM books
```

Task 15: **Branch Performance Report**  
Create a query that generates a performance report for each branch, showing the number of books issued, the number of books returned, and the total revenue generated from book rentals.

```
CREATE TABLE branch_reports
AS
SELECT 
    b.branch_id,
    b.manager_id,
    COUNT(ist.issued_id) as number_book_issued,
    COUNT(rs.return_id) as number_of_book_return,
    SUM(bk.rental_price) as total_revenue
FROM issued_status as ist
JOIN 
employees as e
ON e.emp_id = ist.issued_emp_id
JOIN
branch as b
ON e.branch_id = b.branch_id
LEFT JOIN
return_status as rs
ON rs.issued_id = ist.issued_id
JOIN 
books as bk
ON ist.issued_book_isbn = bk.isbn
GROUP BY 1, 2;

SELECT * FROM branch_reports;
```

CTAS: **Create a Table of Active Members**  
Use the CREATE TABLE AS (CTAS) statement to create a new table active_members containing members who have issued at least one book in the last 7 months.

```
CREATE TABLE active_members
AS
SELECT * FROM members
WHERE member_id IN (SELECT 
                        DISTINCT issued_member_id   
                    FROM issued_status
                    WHERE 
                        issued_date >= CURRENT_DATE - INTERVAL '7 month'
                    )
;

SELECT * FROM active_members;
```

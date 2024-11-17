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

Task 8: **List Members Who Registered in the Last 180 Days**:

```
SELECT *
FROM members
WHERE reg_date > CURRENT_DATE - INTERVAL '180 days';
```

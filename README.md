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

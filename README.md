# 📚 Library Management System - SQL Project

## 📌 Project Overview

The **Library Management System** is a relational database project developed using SQL to manage library operations efficiently. The system tracks books, members, employees, branches, book issuance, and returns while generating analytical reports that help monitor library performance.

This project demonstrates practical SQL skills including:

- Database Design
- Data Modeling
- CRUD Operations
- Joins
- Aggregations
- Window Functions
- Common Table Expressions (CTEs)
- Stored Procedures
- Performance Reporting
- Business Analytics

---

## 🎯 Business Problem

Traditional library record management often suffers from:

- Difficulty tracking issued books
- Lack of visibility into overdue returns
- Manual revenue calculations
- Limited branch performance monitoring
- Inefficient member activity tracking

This SQL solution centralizes all library operations and provides analytical insights for data-driven decision-making.

---

## 🛠️ Tech Stack

| Technology | Purpose |
|------------|----------|
| PostgreSQL | Database Management |
| SQL | Querying & Analysis |
| pgAdmin | Database Administration |
| ER Modeling | Database Design |

---

## 🗂️ Database Schema

The project consists of six core tables:

### Branch

Stores branch information and manager details.

### Employees

Contains employee records and branch assignments.

### Members

Maintains library member registrations.

### Books

Stores book inventory and availability status.

### Issued Status

Tracks issued books and borrowing history.

### Return Status

Maintains returned book records.

---

## 🏗️ Entity Relationship Diagram (ERD)

> Add your ERD image here

```text
branch
   │
   ├── employees
   │
issued_status
   │
   ├── members
   │
   └── books
           │
           └── return_status
```

---

## 📊 Key SQL Concepts Demonstrated

### Database Design

- Primary Keys
- Foreign Keys
- Referential Integrity

### Data Manipulation

- INSERT
- UPDATE
- DELETE

### Data Retrieval

- INNER JOIN
- LEFT JOIN
- GROUP BY
- HAVING
- ORDER BY

### Advanced SQL

- CTAS (Create Table As Select)
- Window Functions
- Stored Procedures
- Aggregations
- Date Functions
- Conditional Logic

---

## 🚀 Database Setup

### Create Database

```sql
CREATE DATABASE library_db;
```

### Core Tables

- Branch
- Employees
- Members
- Books
- Issued Status
- Return Status

The database is fully normalized and uses primary and foreign key constraints to maintain data integrity.

---

## 📈 Business Analysis Performed

### Book Analytics

- Most borrowed books
- Category-wise revenue generation
- Book availability tracking

### Member Analytics

- Active members
- Inactive members
- Frequent borrowers
- Overdue borrowers

### Branch Analytics

- Branch-wise revenue
- Books issued per branch
- Return rate analysis

### Employee Analytics

- Top-performing employees
- Books processed by employee
- Employee productivity ranking

---

# 📋 SQL Tasks & Solutions

## CRUD Operations

### Task 1: Add a New Book

```sql
INSERT INTO books
(
    isbn,
    book_title,
    category,
    rental_price,
    status,
    author,
    publisher
)
VALUES
(
    '978-1-60129-456-2',
    'To Kill a Mockingbird',
    'Classic',
    6.00,
    'yes',
    'Harper Lee',
    'J.B. Lippincott & Co.'
);
```

### Task 2: Update Member Address

```sql
UPDATE members
SET member_address = '125 Oak St'
WHERE member_id = 'C103';
```

### Task 3: Delete Issued Record

```sql
DELETE FROM issued_status
WHERE issued_id = 'IS121';
```

### Task 4: Retrieve Books Issued by Employee

```sql
SELECT *
FROM issued_status
WHERE issued_emp_id = 'E101';
```

### Task 5: Members Who Issued More Than One Book

```sql
SELECT
    issued_member_id,
    COUNT(*) AS total_books
FROM issued_status
GROUP BY issued_member_id
HAVING COUNT(*) > 1;
```

---

## CTAS Operations

### Task 6: Book Issue Count Summary

```sql
CREATE TABLE book_issued_cnt AS
SELECT
    b.isbn,
    b.book_title,
    COUNT(ist.issued_id) AS issue_count
FROM issued_status ist
JOIN books b
ON ist.issued_book_isbn = b.isbn
GROUP BY b.isbn, b.book_title;
```

---

## Advanced Analytics

### Task 7: Retrieve Books by Category

```sql
SELECT *
FROM books
WHERE category = 'Classic';
```

### Task 8: Category-Wise Rental Revenue

```sql
SELECT
    b.category,
    COUNT(*) AS total_transactions,
    SUM(b.rental_price) AS total_revenue
FROM issued_status ist
JOIN books b
ON ist.issued_book_isbn = b.isbn
GROUP BY b.category
ORDER BY total_revenue DESC;
```

### Task 9: Members Registered in Last 180 Days

```sql
SELECT *
FROM members
WHERE reg_date >= CURRENT_DATE - INTERVAL '180 days';
```

### Task 10: Employee and Branch Details

```sql
SELECT
    e.emp_id,
    e.emp_name,
    e.position,
    e.salary,
    b.branch_id,
    b.branch_address,
    manager.emp_name AS manager_name
FROM employees e
JOIN branch b
ON e.branch_id = b.branch_id
JOIN employees manager
ON manager.emp_id = b.manager_id;
```

---

## Stored Procedures

### Issue Book Procedure

Features:

- Checks availability
- Issues book
- Updates inventory status
- Generates notification

### Return Book Procedure

Features:

- Records returned books
- Updates book availability status
- Maintains return history
- Generates return confirmation messages

---

### Task 13: Identify Members with Overdue Books

```sql
SELECT
    ist.issued_member_id,
    m.member_name,
    bk.book_title,
    ist.issued_date,
    CURRENT_DATE - ist.issued_date AS overdue_days
FROM issued_status ist
JOIN members m
    ON m.member_id = ist.issued_member_id
JOIN books bk
    ON bk.isbn = ist.issued_book_isbn
LEFT JOIN return_status rs
    ON rs.issued_id = ist.issued_id
WHERE rs.return_date IS NULL
AND (CURRENT_DATE - ist.issued_date) > 30;
```
---


---

#### 14: Book Return Management

```md
### Task 14: Update Book Status on Return

```sql
UPDATE books b
SET status = 'yes'
FROM issued_status ist
JOIN return_status rs
    ON rs.issued_id = ist.issued_id
WHERE b.isbn = ist.issued_book_isbn;
```
---

## 📊 Additional SQL Analysis

### Top 10 Most Borrowed Books

```sql
SELECT
    b.book_title,
    b.category,
    COUNT(*) AS total_issues
FROM issued_status ist
JOIN books b
ON ist.issued_book_isbn = b.isbn
GROUP BY b.book_title, b.category
ORDER BY total_issues DESC
LIMIT 10;
```

### Monthly Library Activity Trend

```sql
SELECT
    DATE_TRUNC('month', issued_date) AS month,
    COUNT(*) AS books_issued
FROM issued_status
GROUP BY 1
ORDER BY 1;
```

### Top Revenue Generating Categories

```sql
SELECT
    b.category,
    COUNT(*) AS total_transactions,
    SUM(b.rental_price) AS total_revenue
FROM issued_status ist
JOIN books b
ON ist.issued_book_isbn = b.isbn
GROUP BY b.category
ORDER BY total_revenue DESC;
```

### Member Ranking Using Window Function

```sql
SELECT
    m.member_id,
    m.member_name,
    COUNT(ist.issued_id) AS books_borrowed,
    RANK() OVER(
        ORDER BY COUNT(ist.issued_id) DESC
    ) AS member_rank
FROM members m
JOIN issued_status ist
ON m.member_id = ist.issued_member_id
GROUP BY m.member_id, m.member_name;
```

### Branch Performance Dashboard

```sql
SELECT
    b.branch_id,
    COUNT(DISTINCT ist.issued_id) AS books_issued,
    COUNT(DISTINCT rs.return_id) AS books_returned,
    ROUND(
        COUNT(DISTINCT rs.return_id)::NUMERIC /
        COUNT(DISTINCT ist.issued_id) * 100,
        2
    ) AS return_rate
FROM branch b
JOIN employees e
ON b.branch_id = e.branch_id
JOIN issued_status ist
ON e.emp_id = ist.issued_emp_id
LEFT JOIN return_status rs
ON ist.issued_id = rs.issued_id
GROUP BY b.branch_id;
```

### Identify Inactive Members

```sql
SELECT
    m.member_id,
    m.member_name,
    MAX(ist.issued_date) AS last_activity
FROM members m
LEFT JOIN issued_status ist
ON m.member_id = ist.issued_member_id
GROUP BY m.member_id, m.member_name
HAVING MAX(ist.issued_date) < CURRENT_DATE - INTERVAL '6 months'
    OR MAX(ist.issued_date) IS NULL;
```

---

## 📌 Key Performance Indicators (KPIs)

| KPI | Description |
|------|------------|
| Total Revenue | Rental income generated |
| Active Members | Members borrowing books recently |
| Return Rate | Percentage of books returned |
| Most Borrowed Books | High-demand inventory |
| Branch Revenue | Revenue generated by each branch |
| Employee Productivity | Books processed by employees |

---

## 📁 Project Structure

```text
Library-Management-System/
│
├── Database_Setup.sql
├── Library_Analysis.sql
├── Stored_Procedures.sql
├── ERD.png
├── README.md
│
└── Reports/
    ├── Branch_Report.sql
    ├── Revenue_Analysis.sql
    └── Member_Analytics.sql
```

---

## 🎓 Skills Demonstrated

- SQL Query Optimization
- Relational Database Design
- Data Cleaning
- Data Analysis
- Business Intelligence Reporting
- Stored Procedure Development
- Window Functions
- Data Aggregation
- PostgreSQL
- PostgreSQL Stored Procedures (PL/pgSQL)
- Common Table Expressions (CTEs)
- Business KPI Development
- Operational Reporting
- Library Circulation Analytics
- Database Normalization

---

## 🚀 Future Enhancements

- Fine Management System
- Book Reservation Module
- Power BI Dashboard Integration
- Automated Email Notifications
- Role-Based Access Control
- Real-Time Inventory Tracking

---

## 🏆 Project Outcome

Successfully designed and implemented a fully normalized Library Management System capable of:

- Managing library operations efficiently
- Tracking inventory availability
- Monitoring member activity
- Tracking book circulation
- Managing book issuance and returns
- Detecting overdue borrowings
- Generating branch performance reports
- Identifying active library members
- Evaluating employee productivity
- Supporting operational decision-making through SQL analytics

This project demonstrates practical SQL skills commonly required for:

- Data Analyst
- SQL Developer
- Business Analyst
- Database Developer
- Business Intelligence Analyst

---

## 👨‍💻 Author

**Yash Prajapati**

### Connect With Me

- GitHub: https://github.com/yourusername
- LinkedIn: https://linkedin.com/in/yourprofile

---

⭐ If you found this project useful, consider giving it a star on GitHub.

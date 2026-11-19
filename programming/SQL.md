# SQL — Structured Query Language

> SQL is a language used to communicate with relational databases. It allows you to retrieve, insert, update, and delete data.

## 1. TL;DR

- SQL is used to manage and query relational databases.
- A database contains tables.
- Tables contain rows (records) and columns (attributes).
- A primary key uniquely identifies each record.
- A foreign key establishes a relationship between tables.
- SQL is declarative: you describe what data you want, rather than how to retrieve it.

## 2. Basic SQL Operations

| Operation | Purpose |
|---|---|
| SELECT | Retrieve data |
| INSERT | Add records |
| UPDATE | Modify records |
| DELETE | Remove records |
| CREATE | Create database objects |
| ALTER | Modify database objects |
| DROP | Delete database objects |

## 3. Basic Syntax

### SELECT — Retrieve data

```sql
SELECT * FROM users;

SELECT name, email
FROM users;
```

`*` selects all columns.

### WHERE — Filter records

```sql
SELECT *
FROM users
WHERE age >= 18;
```

Common operators:

| Operator | Meaning |
|---|---|
| = | Equal |
| != or <> | Not equal |
| > | Greater than |
| < | Less than |
| >= | Greater than or equal |
| <= | Less than or equal |
| BETWEEN | Within a range |
| IN | Matches a list of values |
| LIKE | Pattern matching |
| IS NULL | Checks for NULL |

### AND / OR / NOT

```sql
SELECT *
FROM users
WHERE age >= 18 AND city = 'Tokyo';
```

### ORDER BY — Sort results

```sql
SELECT *
FROM users
ORDER BY age DESC;
```

- ASC = Ascending (default)
- DESC = Descending

### LIMIT — Restrict results

```sql
SELECT *
FROM users
LIMIT 10;
```

Returns up to 10 records. Syntax varies between database systems.

## 4. Aggregate Functions

Functions that calculate values across multiple records.

| Function | Purpose |
|---|---|
| COUNT() | Count records |
| SUM() | Calculate total |
| AVG() | Calculate average |
| MAX() | Find maximum |
| MIN() | Find minimum |

Example:

```sql
SELECT AVG(age)
FROM users;
```

### GROUP BY

Groups records with the same values.

```sql
SELECT city, COUNT(*)
FROM users
GROUP BY city;
```

### HAVING

Filters grouped results.

```sql
SELECT city, COUNT(*)
FROM users
GROUP BY city
HAVING COUNT(*) > 5;
```

**WHERE vs HAVING:**
- WHERE filters individual records before grouping.
- HAVING filters groups after GROUP BY.

## 5. JOIN — Combine Tables

JOIN retrieves related data from multiple tables.

| JOIN | Result |
|---|---|
| INNER JOIN | Only matching records |
| LEFT JOIN | All records from left table + matching right records |
| RIGHT JOIN | All records from right table + matching left records |
| FULL OUTER JOIN | All records from both tables, matching where possible |

Example:

```sql
SELECT users.name, orders.product
FROM users
INNER JOIN orders
ON users.id = orders.user_id;
```

Here, `users.id` is related to `orders.user_id`.

## 6. Data Modification

### INSERT

```sql
INSERT INTO users (name, age)
VALUES ('Alex', 25);
```

### UPDATE

```sql
UPDATE users
SET age = 26
WHERE id = 1;
```

### DELETE

```sql
DELETE FROM users
WHERE id = 1;
```

**Warning:** UPDATE and DELETE without WHERE can affect every record in the table.

## 7. Database Structure

### Primary Key (PK)
- Uniquely identifies each row.
- Cannot contain NULL.
- Must be unique.

### Foreign Key (FK)
- References a key in another table.
- Establishes relationships between tables.
- Helps maintain referential integrity.

### Constraints

| Constraint | Purpose |
|---|---|
| PRIMARY KEY | Unique row identifier |
| FOREIGN KEY | Enforces a relationship |
| NOT NULL | Prevents NULL values |
| UNIQUE | Prevents duplicate values |
| DEFAULT | Sets a default value |
| CHECK | Enforces a condition |

## 8. Common Mistakes

- Using `=` instead of `IS NULL` to check for NULL.
- Forgetting WHERE in UPDATE or DELETE.
- Confusing WHERE with HAVING.
- Using INNER JOIN when LEFT JOIN is required.
- Forgetting that SQL strings usually require single quotes.
- Assuming all database systems support identical SQL syntax.

## 9. Further Topics to Learn

- [ ] Subqueries
- [ ] SQL aliases (AS)
- [ ] DISTINCT
- [ ] CASE expressions
- [ ] UNION / UNION ALL
- [ ] Indexes
- [ ] Database normalization
- [ ] Transactions (COMMIT / ROLLBACK)
- [ ] ACID properties
- [ ] Views
- [ ] Stored procedures

## 10. Useful Resources

- https://www.postgresql.org/docs/
- https://dev.mysql.com/doc/
- https://sqlbolt.com/

## Personal Notes / Questions

Add explanations of concepts that are difficult to understand, mistakes encountered during practice, and examples tested personally.

---

**Last reviewed:** 2026-11-19

**Knowledge level:** Beginner

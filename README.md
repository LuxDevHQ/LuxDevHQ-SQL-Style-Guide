### **LuxDevHQ Modern SQL Style Guide (2025 Edition)**

---

At [LuxDevHQ](LUXDEVHQ.AI) ourr mission is to equip Africa's next generation of data professionals with world-class skills in data science, data engineering, artificial intelligence, and modern software development.Through rigorous training, project-based learning, and an active alumni network, we ensure our learners don't just understand concepts, they apply them at scale.

This style guide is written collaboratively by engineers and technical trainers at [LuxDevHQ](LUXDEVHQ.AI), each with extensive experience in analytics engineering, data modeling, query optimization, ETL pipelines, and BI reporting. It serves as a comprehensive, pragmatic, and opinionated reference for all SQL practitioners — from students to professionals working in high-scale environments.

---

# Table of Contents

1. Introduction  
2. Principles and Goals  
3. Capitalization and Case Rules  
4. Naming Conventions  
5. Formatting Queries  
6. Comments and Documentation  
7. Data Types and Schema Design  
8. Writing Secure and Performant SQL  
9. Joins and Set Operations  
10. Subqueries and CTEs  
11. Aggregations and Window Functions  
12. Error Handling and NULLs  
13. Indexing and Optimization  
14. Transaction Management  
15. Version Control and SQL Files  
16. Testing and Validation  
17. Code Reviews and Team Practices  
18. Tooling and Automation  
19. Common Pitfalls and Anti-Patterns  
20. Glossary and Resources  

---

# 1. Introduction

SQL (Structured Query Language) is the backbone of data manipulation in relational databases. It powers everything from dashboards to real-time analytics and machine learning feature engineering. Poorly written SQL can degrade performance, increase debugging time, and introduce subtle bugs into your data products.

This guide promotes writing SQL that is:

- Clear to read and reason about
- Consistent across teams and projects
- Performant even on large datasets
- Easily maintainable and testable

---

# 2. Principles and Goals

- **Clarity over cleverness**  
- **Consistency over convenience**  
- **Documentation is not optional**  
- **Simplicity scales**  

---

# 3. Capitalization and Case Rules

✅ SQL keywords in `UPPERCASE`  
✅ Identifiers in `snake_case`

```sql
-- Good
SELECT user_id, full_name FROM users WHERE is_active = TRUE;

-- Bad
select UserID, FullName from Users where IsActive = true;
```

---

# 4. Naming Conventions

| Type     | Convention        | Example            |
|----------|-------------------|--------------------|
| Table    | singular, noun    | `order`, `user`    |
| Column   | snake_case        | `created_at`       |
| Boolean  | `is_`, `has_`     | `is_active`, `has_paid` |
| Index    | idx_ prefix       | `idx_orders_user_id` |

---

# 5. Formatting Queries

Structure queries with indentation and clarity:

```sql
SELECT
    o.order_id,
    o.total_amount,
    u.email
FROM
    orders o
INNER JOIN users u ON o.user_id = u.user_id
WHERE
    o.status = 'delivered'
    AND o.created_at >= CURRENT_DATE - INTERVAL '30 days'
ORDER BY
    o.created_at DESC;
```

---

# 6. Comments and Documentation

Use `--` for line comments. Explain intent, not syntax:

```sql
-- Get total revenue for the last 30 days
SELECT SUM(total_amount) FROM orders WHERE created_at > CURRENT_DATE - INTERVAL '30 days';
```

---

# 7. Data Types and Schema Design

Be explicit with data types:

```sql
CREATE TABLE payments (
    id SERIAL PRIMARY KEY,
    user_id INT NOT NULL,
    amount NUMERIC(10, 2) NOT NULL,
    status VARCHAR(20) NOT NULL CHECK (status IN ('pending', 'paid', 'failed')),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---

# 8. Writing Secure and Performant SQL

- Avoid `SELECT *`
- Always parameterize inputs
- Never trust user input in raw SQL

---

# 9. Joins and Set Operations

```sql
SELECT
    u.full_name,
    COUNT(o.order_id) AS total_orders
FROM
    users u
LEFT JOIN orders o ON u.user_id = o.user_id
GROUP BY u.full_name;
```

---

# 10. Subqueries and CTEs

```sql
WITH top_customers AS (
    SELECT user_id, SUM(total_amount) AS total_spent
    FROM orders
    GROUP BY user_id
    HAVING SUM(total_amount) > 5000
)
SELECT u.full_name, t.total_spent
FROM top_customers t
JOIN users u ON t.user_id = u.user_id;
```

---

# 11. Aggregations and Window Functions

```sql
SELECT
    user_id,
    COUNT(*) AS order_count,
    RANK() OVER (ORDER BY COUNT(*) DESC) AS user_rank
FROM orders
GROUP BY user_id;
```

---

# 12. Error Handling and NULLs

```sql
SELECT
    user_id,
    COALESCE(discount, 0) AS applied_discount
FROM invoices;
```

---

# 13. Indexing and Optimization

```sql
CREATE INDEX idx_orders_created_at ON orders(created_at);

EXPLAIN ANALYZE
SELECT * FROM orders WHERE created_at > CURRENT_DATE - INTERVAL '1 month';
```

---

# 14. Transaction Management

```sql
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

---

# 15. Version Control and SQL Files

```
sql/
├── 001_create_users.sql
├── 002_create_orders.sql
├── 003_add_indexes.sql
```

---

# 16. Testing and Validation

Use dbt tests or assertions:

```sql
-- Null check
SELECT * FROM orders WHERE order_id IS NULL;

-- Foreign key orphan check
SELECT * FROM orders WHERE user_id NOT IN (SELECT user_id FROM users);
```

---

# 17. Code Reviews and Team Practices

- Every query should be peer-reviewed.
- Run `SQLFluff lint` before commit.
- Use a shared staging database for test runs.

---

# 18. Tooling and Automation

- **SQLFluff** — syntax linter and formatter
- **dbt** — analytics engineering framework
- **DBeaver/DataGrip** — SQL IDEs
- **Airflow** — orchestration
- **Metabase/Superset** — BI dashboards

---

# 19. Common Pitfalls and Anti-Patterns

❌ `SELECT *`  
❌ Complex logic in `JOIN ON`  
❌ Not handling `NULL` explicitly  
❌ Unindexed filter columns

---

# 20. Glossary and Resources

- **DDL**: Data Definition Language
- **DML**: Data Manipulation Language
- **SARGable**: Search Argument-able
- **CTE**: Common Table Expression

**Resources**:
- [sqlstyle.guide](https://www.sqlstyle.guide/)
- [PostgreSQL Docs](https://www.postgresql.org/docs/)
- [SQLFluff Docs](https://docs.sqlfluff.com/)
- [dbt Docs](https://docs.getdbt.com/)

---


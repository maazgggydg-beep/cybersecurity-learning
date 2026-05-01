# 🔗 SQL UNION Attack (UNION-based SQL Injection)

A **SQL UNION attack** is a type of SQL injection where an attacker abuses the `UNION` / `UNION ALL` operator to **combine the results of a malicious `SELECT`** with the results of the application's intended query.

## Why `UNION` is Useful to Attackers

`UNION` lets you stack query results together:

- The first `SELECT` is the query the app intended to run.
- The second `SELECT` is attacker-controlled.

If the app displays the result rows, then the attacker can display:
- Usernames and password hashes
- Emails, API keys, tokens
- DB version, current user
- Table and column names (depending on DB)

## Typical Vulnerable Pattern

**Unsafe code (conceptual):**
```sql
SELECT name, price FROM products WHERE category = 'Gifts' AND released = 1;
```

If the application builds it like this:
```
... WHERE category = '" + input + "' ...
```

Then attacker input becomes part of SQL.

## Core Requirements for a UNION Attack

To successfully use `UNION`, the attacker generally needs:

1. **The same number of columns** in both `SELECT` statements
2. **Compatible data types** in each column position
3. A place in the application where **results are reflected** (rendered) to the response

## Step-by-Step: Finding a Working UNION Payload

Assume a parameter like:
```
GET /products?category=Gifts
```

And the backend query becomes:
```sql
... WHERE category = 'Gifts'
```

### 1) Confirm Injection

A simple quote can break the query:
```
Gifts'
```

If the page errors or behavior changes, injection is likely.

### 2) Find the Number of Columns

**Method A: `ORDER BY` Probing:**
```
Gifts' ORDER BY 1--
Gifts' ORDER BY 2--
Gifts' ORDER BY 3--
```

When it fails, you have exceeded the column count.

**Method B: `UNION SELECT` with increasing columns:**
```
Gifts' UNION SELECT NULL--
Gifts' UNION SELECT NULL,NULL--
Gifts' UNION SELECT NULL,NULL,NULL--
```

The first one that succeeds indicates the right column count.

### 3) Identify Which Columns are Reflected

Once you know the column count, inject visible markers:
```
Gifts' UNION SELECT 'AAA','BBB','CCC'--
```

If the page prints `BBB`, then the second column is reflected.

## Attack Examples

### Example A: Extract Database Version

```
Gifts' UNION SELECT @@version, NULL--
```

### Example B: Dump Users Table

```
Gifts' UNION SELECT username, password_hash FROM users--
```

## Common Reasons UNION Attacks Fail

- Wrong number of columns
- Data type mismatch
- Results not displayed by app
- WAF blocking keywords
- Query uses different structure
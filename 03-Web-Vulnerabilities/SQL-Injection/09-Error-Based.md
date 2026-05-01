# ❌ Exploiting Blind SQL Injection by Triggering Conditional Errors

Sometimes an application is vulnerable to SQL injection, but its response doesn't change. In that case, older techniques like adding different boolean conditions don't work. Instead, we craft a query that triggers a database error only when our condition is true.

## Core Concept

We use a `CASE` statement to divide by zero (or trigger another error) when our condition is true.

## Payload Example

```sql
XYZ' AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE 'a' END)='a
```

**The Condition:** `1=1` (This is where you'd actually put your test, like `username='admin'`).

**The "Bomb":** `THEN 1/0`. Since 1=1 is true, the database tries to divide by zero.

**The Result:** The database throws an error, the web server fails to handle it, and you get an Error Page.

## Payload Comparison

```sql
xyz' AND (SELECT CASE WHEN (1=2) THEN 1/0 ELSE 'a' END)='a'
xyz' AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE 'a' END)='a'
```

In the first payload, the condition is `1=2`, which is false, so the whole `SELECT CASE` evaluates to `'a'` and the website loads normally. In the second payload, `1=1` is true, so the database tries `1/0`, causing an error.

If the error causes a difference in the application's HTTP response, you can use this to determine whether the injected condition is true.

## Using This Technique to Extract Data

You can retrieve data by testing one character at a time:

```sql
xyz' AND (SELECT CASE WHEN (Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') THEN 1/0 ELSE 'a' END FROM Users)='a
```

In this scenario, we are trying to guess the first letter of the password for the user **Administrator**. We use the `CASE` function (which acts like an **IF-ELSE** statement in SQL) to trigger an error only when our condition is true.

## The Logic

The query checks if the first letter of the Administrator's password is **greater than 'm'**.

- **IF TRUE:** It executes `1/0` (division by zero). This causes a database error, which makes the website return an **Error Page** (like a 500 Internal Server Error).
- **IF FALSE:** It executes the `ELSE` part, which returns the letter `'a'`. The query then finishes normally (e.g., `'a'='a'`), so the **website loads normally**.

## Why Use `>` Instead of `=`

We use the "greater than" operator (`>`) because it is much **faster** to find the character. This is called a **binary search**. Instead of checking every single letter one by one (`= 'a'`, `= 'b'`, `= 'c'`...), we:

1. Check if char > 'm' (divide in half)
2. If true, check if char > 't' (narrow further)
3. Continue until isolated

This reduces ~26 checks per character to just ~5-6 checks.

## Oracle-Specific Payloads

Oracle uses different syntax:

```sql
' || (SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM DUAL) || '
' || (SELECT CASE WHEN (username='administrator' AND SUBSTR(password,1,1)='a') THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE rownum=1) || '
```

When working with Oracle, use these modified payloads.

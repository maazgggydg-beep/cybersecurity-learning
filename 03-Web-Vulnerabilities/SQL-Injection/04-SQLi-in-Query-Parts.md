# 🔀 SQL Injection in Different Query Parts

## 1. Injection in UPDATE Statements

UPDATE queries change existing information. Usually, they look like this:
```sql
UPDATE users SET bio = '[USER_INPUT]' WHERE id = [USER_ID];
```

**How it's exploited:** You can use a single quote to "break out" of the `bio` field and start writing your own commands.

**The Attack:** If you enter `' , role='admin' --` into your bio field, the query becomes:
```sql
UPDATE users SET bio = '' , role='admin' --' WHERE id = 123;
```

**Why it is dangerous:** The `--` tells the database to ignore everything after it. This means the `WHERE` clause is deleted, and the database might update **every user** in the system to be an "admin".

## 2. Injection in `INSERT` Statements

`INSERT` queries create new records, like when you sign up. They look like:
```sql
INSERT INTO users (username, password, bio) VALUES ('[USER_INPUT]', 'pass123', 'hello');
```

**How it's exploited:** You can leak data by making it appear in a field you can actually see later.

**The Attack:** In the `username` field, you might enter:
```
admin', 'password', (SELECT version())) --
```

**The Result:** The database creates a new user, but in the bio column (which you can see on your profile page), it saves the actual **database version**.

## 3. Injection in Table or Column Names

Sometimes an application lets you choose what to view (e.g., a dropdown to View Products or View Users). The query looks like:
```sql
SELECT * FROM [USER_INPUT];
```

**How it's exploited:** Instead of just choosing from a list, you manually change the name to a hidden table.

**The Attack:** If the URL says `?table=products`, you change it to `?table=users`.

**Why it's unique:** You can't use standard "tricks" like `OR 1=1` here because you are changing the **structure** of the query itself, not just the data.

## 4. Injection in `ORDER BY` Clauses

This happens when you click a column header to sort a list (e.g., sorting products by price). The query looks like:
```sql
SELECT * FROM products ORDER BY [USER_INPUT];
```

**How it's exploited:** You can't use `UNION` attacks here, so hackers use **Boolean** or **Time-Based** logic to "ask" the database questions.

**The Attack:** You can inject a logic test:
```sql
ORDER BY (CASE WHEN (1=1) THEN price ELSE id END)
```

**The Result:** If the list sorts by "price," you know your test `(1=1)` was true. If it sorts by "id," it was false. By doing this thousands of times, you can "read" the database character by character.

## Summary Table of Risks

| Location | Intent | Impact |
|----------|--------|--------|
| **UPDATE** | Modify data | Can change *any* user's password or role. |
| **INSERT** | Leak data | Shows hidden data on your profile or public pages. |
| **Table/Column** | Access tables | View "internal" tables the developer didn't list. |
| **ORDER BY** | Blind discovery | Stealthily leak data through sorting behavior. |

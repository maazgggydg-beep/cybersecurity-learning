# 🔍 How to Detect SQL Injection

Think of manual SQL injection testing as "poking" the application to see if you can break its logic or force the database to talk back to you.

Here is a breakdown of methods:

## 1. The Single Quote (`'`)

This is the stress test. Most SQL queries use single quotes to wrap data. By submitting one, you're trying to prematurely close the developer's quote.

**The Goal:** If the app returns a "500 Internal Server Error" or a database-specific error message, it means your quote broke the code—a huge red flag that the input isn't being sanitized.

## 2. Logic & Math (Base Values)

You test if the app can "calculate" your input.

**The Test:** If a product page is `id=1`, try changing it to `id=2-1`

**The Goal:** If the page still loads product #1, the database performed the math (`2 minus 1`). This proves your input is being executed as code, not just read as text.

## 3. Boolean Conditions (`OR 1=1`)

This tests the "truth" of a page.

**The Test:** Search for a username with `' OR 1=1 --`.

**The Goal:** Since `1=1` is always true, the database might ignore the password check and log you in as the first user in the table. If `' OR 1=2` (which is false) gives a different result, you know the query logic is being executed.

## 4. Time Delays

Sometimes the app is "blind" in such a way it won't show errors or different text.

**The Test:** You send a command like `'; WAITFOR DELAY '0:0:10'--`.

**The Goal:** If the website takes exactly 10 seconds longer to load, you know the database received and executed your command. It's like a silent "ping."

## 5. OAST (Out-of-Band)

This is for when the server is completely locked down and won't show errors or delays.

**The Test:** You submit a payload that tells the database to look up a DNS record or visit a URL you control (e.g., `your-collab-link.com`).

**The Goal:** You check your own server logs. If you see an "interaction" from the target's IP address, you've successfully forced their database to reach out to you.
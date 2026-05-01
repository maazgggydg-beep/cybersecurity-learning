# 👁️ Blind SQL Injection (Blind SQLi)

Blind SQL injection happens when an application is vulnerable to SQL injection but does **not** return database errors or query results in the response. You still confirm and exploit the injection by observing other behaviors.

## When to Suspect Blind SQLi

- Inputs affect the page behavior, but you never see SQL errors or output
- The same request sometimes returns different content, status codes, redirects, or response times
- Parameters in URLs, form fields, cookies, or headers seem to influence logic

## Two Main Types

### 1) Boolean-based

You change the query so the page response is different depending on whether a condition is **true** or **false**.

- **True condition** → page looks normal
- **False condition** → page changes (missing content, different message, empty results)

### 2) Time-based

You inject a condition that causes the database to **delay** the response only when the condition is true.

- If response is slow → condition was true
- If response is normal → condition was false

## High-Level Exploitation Idea

You can extract data one character at a time:

1. Confirm the injection point
2. Find a reliable true vs false signal
3. Enumerate data (database name, tables, columns, values)
4. Use binary search to reduce requests

## Common Pitfalls

- **Unstable timing** - Network jitter causes false positives
- **Caching/WAF** - May alter responses or block patterns
- **Different code paths** - False case might trigger different logic
- **Rate limiting** - Extraction is request-heavy
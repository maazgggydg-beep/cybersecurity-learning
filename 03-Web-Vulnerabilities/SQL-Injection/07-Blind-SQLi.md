# 👁️ Blind SQL Injection (Blind SQLi)

Blind SQL injection happens when an application is vulnerable to SQL injection but does **not** return database errors or query results in the response. You still confirm and exploit the injection by observing other behaviors.

## When to Suspect Blind SQLi

- Inputs affect the page behavior, but you never see SQL errors or output
- The same request sometimes returns different content, status codes, redirects, or response times
- Parameters in URLs, form fields, cookies, or headers seem to influence logic (login, search filters, sorting, etc.)

## Two Main Types

### 1) Boolean-based

You change the query so the page response is different depending on whether a condition is **true** or **false**.

- **True condition** → page looks normal
- **False condition** → page changes (missing content, different message, empty results, different HTTP code)

Common signals to compare:

- Length of response body
- Presence or absence of a keyword
- HTTP status code
- Redirect location

### 2) Time-based

You inject a condition that causes the database to **delay** the response only when the condition is true.

- If response is slow → condition was true
- If response is normal → condition was false

Time-based is useful when there is no visible content difference.

## High-Level Exploitation Idea

You can extract data one bit or one character at a time:

1. Confirm the injection point.
2. Find a reliable true vs false signal (content change or timing)
3. Enumerate:
   - Database name
   - Table names
   - Column names
   - Sensitive values (usernames, password hashes, API keys)
4. Use binary search on character codes to reduce requests

## Common Pitfalls

- **Unstable timing**: network jitter can cause false positives in time-based tests.
- **Caching/WAF/CDN**: may alter responses or block repeated patterns
- **Different code paths**: the false case might trigger a different backend logic unrelated to SQL
- **Rate limiting**: extraction is request-heavy

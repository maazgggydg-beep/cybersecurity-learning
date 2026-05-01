# 📚 Time-Based and OAST Reference Guide

## 1. Time-Based Blind SQL Injection

This method relies on forcing the database to wait before sending a response. If the website takes a long time to load after your "poke," you've confirmed it's vulnerable.

**How it works:** You use a command like `SLEEP()` or `WAITFOR DELAY`

**The Logic:** If the first letter of the admin's password is 'A', then wait 10 seconds. If not, respond immediately.

### Example Payloads by Database

- **MySQL:** `1' AND (SELECT 1 FROM (SELECT(SLEEP(10)))a)--`
  - Translation: "Hey database, if you see this, take a 10-second nap before you answer me"
  
- **PostgreSQL:** `1' AND pg_sleep(10)--`

- **Microsoft SQL Server (MSSQL):** `1'; WAITFOR DELAY '0:0:10'--`

**Why it's useful:** You use this when the application doesn't show any error messages or change its content, no matter what you type.

---

## 2. Out-of-Band (OAST) SQL Injection

OAST stands for **Out-of-Band Application Security Testing**. This is like tricking the database into calling your phone instead of answering you.

### Key Differences

| Feature | Time-Based | OAST |
|---------|-----------|------|
| Detection | Response timing | Network interaction |
| Speed | Slow (10-30 sec delays) | Fast (instant) |
| Reliability | Network dependent | More reliable |
| Setup | Simple | Requires infrastructure |
| Firewall blocking | Less likely | More likely |

---

## Quick Decision Tree

```
Can you see error messages?
├─ YES → Use error-based blind SQLi
└─ NO → Can you see response differences?
    ├─ YES → Use boolean-based blind SQLi
    └─ NO → Can database make network requests?
        ├─ YES → Use OAST techniques
        └─ NO → Use time-based blind SQLi
```

---

## Tools for OAST Testing

1. **Burp Suite Collaborator** - Integrated into Burp Pro
2. **Interact.sh** - Free OAST platform
3. **Pingback** - Simple DNS callback
4. **Custom DNS/HTTP server** - Full control

---

*Use this guide to choose the right technique for your testing scenario.*

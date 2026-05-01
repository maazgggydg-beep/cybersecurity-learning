# 📚 Time-Based and OAST Reference Guide

## 1. Time-Based Blind SQL Injection

Force database to wait before responding. Long load time = vulnerable.

**How it works:** Use `SLEEP()` or `WAITFOR DELAY`

**The Logic:** If first letter of admin's password is 'A', wait 10 seconds. If not, respond immediately.

### Example Payloads by Database

- **MySQL:** `1' AND (SELECT 1 FROM (SELECT(SLEEP(10)))a)--`
  - "Hey database, take 10-second nap before answering"

- **PostgreSQL:** `1' AND pg_sleep(10)--`

- **Microsoft SQL Server:** `1'; WAITFOR DELAY '0:0:10'--`

**Why useful:** When app doesn't show errors or change content no matter what you type.

---

## 2. Out-of-Band (OAST) SQL Injection

OAST = Out-of-Band Application Security Testing. Like tricking database into calling your phone instead of answering.

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

*Use this guide to choose the right technique for your scenario.*
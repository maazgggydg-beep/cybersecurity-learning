# 🔓 What is SQL Injection?

SQL injection (SQLi) is a vulnerability that allows an attacker to manipulate database queries by injecting malicious SQL through application inputs.

## Common Causes

- Unsafely concatenating user input into SQL strings
- Missing input validation and output encoding
- Over-privileged database accounts

## Impact

- Read sensitive data (e.g., users, passwords, financial records)
- Modify or delete data
- Bypass authentication/authorization
- In some cases, execute administrative actions on the database

## How to Prevent SQLi

- Use parameterized queries / prepared statements
- Avoid dynamic SQL; if needed, strictly whitelist allowed values
- Apply least-privilege permissions for the DB user
- Validate input and handle errors safely (don't expose SQL errors)
- Add security testing (SAST/DAST) and code review checks

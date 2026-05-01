# 🛡️ How to Prevent SQL Injection

You can prevent most instances of SQL injection using parameterized queries instead of string concatenation within the query. These parameterized queries are also known as "prepared statements".

## ❌ Vulnerable Code

The following code is vulnerable to SQL injection because the user input is concatenated directly into the query:

```java
String query = "SELECT * FROM products WHERE category = '" + input + "'";
Statement statement = connection.createStatement();
ResultSet resultSet = statement.executeQuery(query);
```

## ✅ Secure Code (Prepared Statement)

You can rewrite this code in a way that prevents the user input from interfering with the query structure:

```java
PreparedStatement statement = connection.prepareStatement("SELECT * FROM products WHERE category = ?");
statement.setString(1, input);
ResultSet resultSet = statement.executeQuery();
```

## Key Prevention Principles

1. **Always use parameterized queries** - Never concatenate user input
2. **Input validation** - Whitelist allowed characters/formats
3. **Principle of least privilege** - DB users should have minimal permissions
4. **Error handling** - Don't expose SQL errors to users
5. **Security testing** - Include SAST/DAST in your CI/CD pipeline

## Other Prevention Methods

- Escape special characters
- Use object-relational mapping (ORM) frameworks
- Implement WAF (Web Application Firewall) rules
- Regular security audits and code reviews
- Database activity monitoring

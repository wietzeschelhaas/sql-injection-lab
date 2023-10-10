# sql-injection-lab

Blind SQL injection occurs when an application is vulnerable to SQL injection, but its HTTP responses does not contain any SQL query result. So a UNION attack wont work.
Instead, we can infer the results indirectly based on the application's behavior, such as changes in response times or error messages.

In this workshop we will solve two blind SQL injeciton labs, error-based SQL injection and SQL injection with time delays.
The goal for both labs is to log in as the administrator user. 

## error-based SQL injection
Go to the lab website and access the lab.
https://portswigger.net/web-security/sql-injection/blind/lab-conditional-errors

The website we are hacking is a webshop and it has a SQL injection vulnerability in the TrackingId cookie!
Assuming the cookie looks like this :

``` 
TrackingId=xyz, 
```

Modify it to the following. You can use BurpSuite to modify the cookie, or simply use tge DevTools in your browser.

``` 
TrackingId=xyz' AND '1'='1
```

Notice that there is a "Welcome Back" message in the top right corner. Now change it to the following:

``` 
TrackingId=xyz' AND '1'='2
```
Notice that the "Welcome Back" text disappears. This shows that we can use a boolean condition to infer results. If the expression, in this case, '1'='1, evaluates to true, the "Welcome Back" text is shown. If it evaluates to false, the text dissapears. 





``` sql
SELECT a, b FROM table1 UNION SELECT c, d FROM table2
```

``` 
$sqlQuery = "SELECT a, b FROM products WHERE categories = + '" + $category + "'
```

``` sql
UNION SELECT username, password FROM users--'
```

``` sql
SELECT a, b FROM products WHERE categories = '' UNION SELECT username, password FROM users--'
```


# sql-injection-lab
In this workshop we will solve two blind SQL injeciton labs

Blind SQL injection occurs when an application is vulnerable to SQL injection, but its HTTP responses does not contain any SQL query result. So a UNION attack wont work.
Instead, we can infer the results indirectly based on the application's behavior, such as changes in response times, error messages, or other observable behaviors.


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


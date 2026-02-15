
# SQL Injection Fundamentals

## What I learned

### MySQL
- Database Management System helps create, define, host and manage databases
- here are 2 types of data bases `Relational` and `Non-Relational` Databases, and only Relational uses SQL
- Relational Database Management System is superior, easy to learn, understand and very scalable as it allows to link for example `id` to `user_id`, so user information will be retrieve with each post without storing storing user information in each post.
- One of the best thing that I learned and understood well was `SELECT`, `FROM`, `WHERE` `AND` and `OR` . Because it very easy to understand: `SELECT <what are you looking for for example id> FROM <where is that are you looking for> WHERE <condition for example number < 100 >  AND/OR <another condition>.` The difference between `AND` and `OR` is that if AND is specified both condition should be true to output the answer where when using OR only one or another condition should be true to get the result.

### SQL Injections
- it is possible within `PHP` web application to connect to database using `MySQL` as follows:
```php
$conn = new mysqli("localhost", "root", "password", "users");
$query = "select * from logins";
$result = $conn->query($query);
```
The output will be stored at `$result` and we can print it as follows:
```php
while($row = $result->fetch_assoc() ){
    echo $row["name"]."<br>";
}
```

- Other thing that I realized and found very interesting while doing second section of `SQL injections` Is that if I use username `tom` and password `' OR '1'='1` I will log in as admin not user top but if I shuffle it a bit and make username `tom ' OR '1'='1` and password `does_not_matter_what` I will log in as tom. 
	- Then after googling explanation of why is that found that AND executes before OR. So it reads as:  `WHERE (username='tom' AND password='') OR ('1'='1')`. 1=1 is always true -> returns first row in database = admin.
	- Second looks like `SELECT * FROM logins WHERE username='tom' OR '1'='1' AND password='anything'` AND executes first again. `password=something` is false, do the AND fails and falls back to `username=tom` OR `1=1` as user tom exist and `1=1` is always true, so logins as tom.
	- Understood that order is important as if `OR '1'='1'` is last condition it overrides everything →
-  Learned few new very useful things. 
	- `UNION` combines 2 tables with SELECT to one,  but only if they are same type and numbers of columns. 
	- `AS` can define the names
	- `SUM` adds numbers
	- `DESCRIBE` show all the fields and types.
### Exploitation
- Use `.` to  use table for example `users` from a `database` as follows: 
```sql
SELECT * FROM database.users;
```
- Read the file using load file function`LOAD_FILE("path/to/flag.txt")`
## Skills Assessment - SQL Injection Fundamentals
- This assessment was kind of hard for me ass first thing I was tried to do is use SQLi on login page which wasn't vulnerable and I spent a lot of time on it. Eventually looked for the hint and found that register code string is vulnerable so I registered account and logged in.
- Second thing was that I completely forgot that closing sting could be `')` instead of `'`. 
- Right after it I made only silly mistakes as typos forgating to add `'` or space after `--` 
## SQLi Methodology
1. Find in injection point
2. Determine column count
3. Find string columns 
4. Enumerate database via INFORMATION_SCHEMA
5. Dump target data
6. Try file read
7. Try write file

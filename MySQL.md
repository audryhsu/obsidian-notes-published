---
tags: 
aliases: 
---

start mySQL engine
```
mysql.server start
mysql.server stop
```

Enter MySQL monitor terminal
```zsh
sudo mysql # as root OR as a specific user
mysql -D <database name> -u <user> -p
```


## Install database driver
`go-sql-driver/mysql` driver Acts as a middleman to translate commands between Go and MySQL database

## Data types
Interesting discussion about `DATETIME` vs `TIMESTAMP` on [SO](https://stackoverflow.com/questions/409286/should-i-use-the-datetime-or-timestamp-data-type-in-mysql).
- `DATETIME` requires timezone context to have meaning, and is a relative measure of a point in time.
	- 8 bytes as opposed to TIMESTAMP 4 bytes
	- MySQL field, so you can do calculations on it, but timezone is a pain and requires "mysql" understanding of datetime
- `TIMEZONE` is number of miliseconds since Unix epoch --> well-defined, absolute point in time
	- good for precision and recording when exactly something occurred
	- Ex: When changing server's `time_zone` setting to UTC, the values will all be updated 
	- as of 2023-05-08, there's a upperbound of the year 2038. Probably will be fixed though.
- `BIGINT` is advocated for, since most databases handle BigInt types more or less the same.

> [!Example] `TIMEZONE` VS `DATETIME`
> ```sql
> +------------------+---------------------+
> | Variable_name    | Value               |
> +------------------+---------------------+
> | system_time_zone | India Standard Time |
> | time_zone        | Asia/Calcutta       |
> +------------------+---------------------+
> 
> TWO COLUMNS:
> -> mydatetime datetime,
> -> mytimestamp timestamp
> 
> mysql> insert into datedemo values ((now()),(now()));
> 
> mysql> select * from datedemo;
> +---------------------+---------------------+
> | mydatetime          | mytimestamp         |
> +---------------------+---------------------+
> | 2011-08-21 14:11:09 | 2011-08-21 14:11:09 |
> +---------------------+---------------------+
> 
> mysql> set time_zone="america/new_york";
> 
> mysql> select * from datedemo;
> +---------------------+---------------------+
> | mydatetime          | mytimestamp         |
> +---------------------+---------------------+
> | 2011-08-21 14:11:09 | 2011-08-21 04:41:09 |
> +---------------------+---------------------+
> ```
> -  When changing server's `time_zone` setting, a date stored in IST timezone will stay that way no matter what
> - Timestamp values are updated to represent seconds since Unix Epoch, a fixed point in time.  
> 
> Overall, you want the client/app to own the responsibility to interpret and display timezones, not the database.


---
Tags: [Databases](./Databases.md)

Reference:

Related: 
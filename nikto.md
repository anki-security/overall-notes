# Nikto

## Basic Nikto Command

```bash
nikto -h <target>
```

- `-h <target>`: Specifies the target IP address, hostname, or URL (e.g., `http://10.10.10.10`).
- Scans the target web server for vulnerabilities and misconfigurations.
- Example: `nikto -h 10.10.10.10`

## Common Nikto Scan Types

Nikto supports various scan configurations for different levels of thoroughness and specificity.

### Basic Scan

```bash
nikto -h http://10.10.10.10
```

- Performs a default scan, checking for common vulnerabilities, outdated software, and misconfigurations.
- Automatically detects the port (80 for HTTP, 443 for HTTPS) unless specified.

### Scan with Specific Port

```bash
nikto -h 10.10.10.10 -p 8081
```

- `-p <port>`: Specifies a non-standard port (e.g., `8081`).
- Useful for scanning web servers running on non-default ports.

### SSL/TLS Scan

```bash
nikto -h https://10.10.10.10 -ssl
```

- `-ssl`: Forces Nikto to use SSL/TLS for HTTPS servers.
- Automatically detects HTTPS if the URL starts with `https://`.

### Scan with Tuning

```bash
nikto -h http://10.10.10.10 -Tuning x
```

- `-Tuning <option>`: Customizes the scan to focus on specific tests (e.g., `x` for all tests except denial-of-service).
- Options: `1` (file upload), `2` (interesting files), `3` (misconfigurations), etc. See `nikto -list-plugins`.

## Useful Nikto Options

- **Target Specification**:
  - `-h <target>`: Single target (IP, hostname, or URL).
  - `-H <file>`: Scan multiple hosts from a file (e.g., `-H targets.txt`).
  - `-p <port>`: Specify port(s) (e.g., `-p 80,443` or `-p 8000-9000`).
- **Authentication**:
  - `-id <username:password>`: Provide credentials for basic authentication (e.g., `-id admin:password`).
  - `-auth <method>`: Specify authentication method (e.g., `basic`, `ntlm`).
- **Output**:
  - `-o <file>`: Save output in HTML format (e.g., `-o report.html`).
  - `-F <format>`: Specify output format (e.g., `-F txt` for text, `-F csv` for CSV).
  - `-Save <directory>`: Save output files to a specific directory.
- **Performance and Stealth**:
  - `-Tuning <option>`: Limit scan types to reduce noise (e.g., `-Tuning 23` for misconfigurations and interesting files).
  - `-Pause <seconds>`: Add delay between requests (e.g., `-Pause 5` to avoid detection).
  - `-evasion <method>`: Use evasion techniques (e.g., `1` for random URI encoding, `2` for directory self-reference).
- **Plugins**:
  - `-Plugins <plugin>`: Run specific plugins (e.g., `-Plugins apacheusers`).
  - `-list-plugins`: List available plugins for targeted scans.
- **Proxy Support**:
  - `-useproxy <proxy>`: Route traffic through a proxy (e.g., `-useproxy http://127.0.0.1:8080` for Burp Suite).

**Example with Advanced Options**:

```bash
nikto -h http://10.10.10.10 -p 8081 -Tuning 23b -o report.html -F html -useproxy http://127.0.0.1:8080
```

- Scans port 8081, focuses on misconfigurations and outdated software, saves output as HTML, and routes through a proxy.

## Nikto Workflow

Follow this step-by-step process for effective web server scanning:

1. **Basic Scan**:

   ```bash
   nikto -h http://10.10.10.10
   ```

   - Performs a quick scan to identify common issues and gather initial information.

2. **Port-Specific Scan**:

   ```bash
   nikto -h 10.10.10.10 -p 8081
   ```

   - Targets a specific port if the web server runs on a non-standard port.

3. **SSL/TLS Scan**:

   ```bash
   nikto -h https://10.10.10.10 -ssl
   ```

   - Scans HTTPS servers, ensuring SSL/TLS is properly handled.

4. **Focused Scan with Tuning**:

   ```bash
   nikto -h http://10.10.10.10 -Tuning 23
   ```

   - Limits the scan to specific tests (e.g., misconfigurations, interesting files).

5. **Comprehensive Scan with Output**:

   ```bash
   nikto -h http://10.10.10.10 -p 80,443,8081 -o report.txt -F txt -Plugins all
   ```

   - Scans multiple ports, saves results in text format, and runs all available plugins.

**Example Workflow**:

```bash
nikto -h http://10.10.10.10
nikto -h 10.10.10.10 -p 8081
nikto -h https://10.10.10.10 -ssl -Tuning 23
nikto -h http://10.10.10.10 -o scan.html -F html -useproxy http://127.0.0.1:8080
```

## Best Practices

1. **Start with Default Scans**: Begin with a basic scan to minimize noise and assess the server’s response.
2. **Use Tuning for Efficiency**: Limit scans with `-Tuning` to focus on relevant tests and reduce scan time.
3. **Save Output**: Always use `-o` or `-Save` to store results for analysis and reporting.
4. **Combine with Other Tools**: Pair Nikto with Nmap, Burp Suite, or Wfuzz for comprehensive testing.
5. **Stay Legal**: Only scan systems with explicit permission to avoid legal issues.
6. **Monitor for False Positives**: Manually verify findings, as Nikto may report outdated or inaccurate vulnerabilities.

## Common Issues and Solutions

- **Firewall/WAF Blocking**: Use `-evasion` (e.g., `-evasion 1`) or `-Pause` to bypass restrictions.
- **Connection Errors**: Verify the target URL, port, and protocol; use `-v` for verbose output to debug.
- **Slow Scans**: Adjust `-Pause` or use `-Tuning` to reduce the number of tests.
- **Authentication Required**: Provide credentials with `-id` or configure proxy authentication in Burp Suite.

## Additional Notes

- **Plugins**: Explore `/usr/share/nikto/plugins/` for additional plugins or create custom ones for specific needs.
- **Database Updates**: Update Nikto’s vulnerability database with `nikto -update` to ensure the latest checks.
- **Verbose Output**: Use `-v` for detailed output during debugging.
- **Documentation**: Run `nikto -h` or visit the official Nikto website for advanced options and plugin details.
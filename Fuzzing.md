# Fuzzing Guide

Fuzzing is a testing technique used to discover vulnerabilities, hidden files, directories, or endpoints in web applications by sending automated requests with varied inputs. This guide focuses on using **Wfuzz** on Kali Linux, including useful wordlists, commands, and filtering options.

## Useful Wordlists in Kali Linux

Kali Linux includes a variety of wordlists under `/usr/share/wordlists/`, particularly from the **SecLists** repository, tailored for web fuzzing:

- **API Endpoints**:
  - `/usr/share/wordlists/seclists/Discovery/Web-Content/api/api-endpoints.txt`: Common API endpoint paths.
  - `/usr/share/wordlists/seclists/Discovery/Web-Content/api/api-endpoints-res.txt`: Additional API endpoint variations.
- **Web Content**:
  - `/usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-files.txt`: Common file names for file enumeration.
  - `/usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-directories.txt`: Common directory names for directory enumeration.

**Tip**: Decompress wordlists if needed using `gunzip` (e.g., `gunzip /usr/share/wordlists/rockyou.txt.gz`).

## Running Wfuzz

**Wfuzz** is a versatile tool for fuzzing web applications, allowing enumeration of directories, files, parameters, and more. The `FUZZ` keyword in the URL is replaced with entries from the specified wordlist.

### Basic Command

```bash
wfuzz -c -w <wordlist> --hc 404 http://<target-ip>/FUZZ
```

- `-c`: Enables colorized output for readability.
- `-w <wordlist>`: Specifies the wordlist path (e.g., `/usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-directories.txt`).
- `--hc 404`: Hides responses with HTTP status code 404 (not found).
- `http://<target-ip>/FUZZ`: Target URL with `FUZZ` as the placeholder for wordlist entries.

**Example**:

```bash
wfuzz -c -w /usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-directories.txt --hc 404 http://10.10.10.10/FUZZ
```

### Advanced Options

- **Thread Control**:
  - `-t <threads>`: Sets the number of concurrent threads (default: 10; e.g., `-t 50` for faster scans).
- **Proxy Support**:
  - `--proxy <ip:port>`: Routes traffic through a proxy (e.g., `--proxy 127.0.0.1:8080` for Burp Suite).
- **Output**:
  - `-o <file>`: Saves results to a file (e.g., `-o results.txt`).
  - Formats: Use `--oF <format>` for CSV, JSON, etc. (e.g., `--oF csv`).

## Useful Filters

Filters refine fuzzing results by including or excluding responses based on HTTP status codes, response sizes, or content.

### Hide Status Codes

- `--hc <code>`: Hides responses with specific status codes.
  - Example: `--hc 404` hides 404 errors.
  - Multiple codes: `--hc 404,403,500` hides 404, 403, and 500 responses.

### Show Status Codes

- `--sc <code>`: Shows only responses with specific status codes.
  - Example: `--sc 200` shows only 200 (OK) responses.
  - Multiple codes: `--sc 200,301` shows 200 and 301 (Redirect) responses.

### Filter by Response Size

- `--hl <lines>`: Hides responses with a specific number of lines.
- `--hw <words>`: Hides responses with a specific number of words.
- `--hs <string>`: Hides responses containing a specific string.
  - Example: `--hs "Not Found"` hides responses with "Not Found".

### Show by Response Size

- `--sl <lines>`: Shows responses with a specific number of lines.
- `--sw <words>`: Shows responses with a specific number of words.

**Example with Filters**:

```bash
wfuzz -c -w /usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-files.txt --sc 200 --hc 404,403 http://10.10.10.10/FUZZ
```

This command shows only 200 responses, hiding 404 and 403 errors.

## Fuzzing Multiple Parameters

To fuzz multiple parts of a request (e.g., URL path and query parameters), use multiple `FUZZ` placeholders with corresponding wordlists.

**Example**:

```bash
wfuzz -c -w wordlist1.txt -w wordlist2.txt http://10.10.10.10/FUZZ?param=FUZZ2
```

- `wordlist1.txt`: Fuzzes the URL path (`FUZZ`).
- `wordlist2.txt`: Fuzzes the query parameter (`FUZZ2`).


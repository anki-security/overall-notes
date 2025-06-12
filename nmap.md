# Nmap Guide

Nmap (Network Mapper) is a powerful open-source tool for network discovery and security auditing. This guide provides a structured overview of using Nmap on Kali Linux, including common commands, scan types, and best practices for network enumeration.

## Overview

Nmap is used to discover hosts, services, open ports, operating systems, and potential vulnerabilities on a network. It supports various scan types and options for flexibility and stealth.

## Prerequisites

- **Permissions**: Ensure you have explicit authorization to scan the target network or system.
- **Installation**: Nmap is pre-installed on Kali Linux. Verify with `nmap --version`.
- **Target**: Identify the target IP address or range (e.g., `10.10.10.10` or `10.10.10.0/24`).

## Basic Nmap Command

```bash
nmap <target-ip>
```

- Scans the target for the top 1000 ports using default settings (TCP SYN scan).
- Example: `nmap 10.10.10.10`

## Common Nmap Scan Types

Nmap supports various scan techniques, each suited for different purposes:

### TCP SYN Scan (Stealth Scan)

```bash
nmap -sS <target-ip>
```

- `-sS`: Performs a TCP SYN scan (default for privileged users).
- Sends SYN packets and analyzes responses without completing TCP handshakes, making it stealthier.
- Example: `nmap -sS 10.10.10.10`

### TCP Connect Scan

```bash
nmap -sT <target-ip>
```

- `-sT`: Performs a full TCP connect scan (default for non-privileged users).
- Completes the TCP handshake, less stealthy but useful when SYN scans are blocked.
- Example: `nmap -sT 10.10.10.10`

### UDP Scan

```bash
nmap -sU <target-ip>
```

- `-sU`: Scans for open UDP ports.
- Slower due to UDP’s connectionless nature and potential rate-limiting.
- Example: `nmap -sU 10.10.10.10`

### Service Version Detection

```bash
nmap -sV <target-ip>
```

- `-sV`: Detects service versions and protocols running on open ports.
- Useful for identifying specific software (e.g., Apache 2.4.29, SSH OpenSSH 7.6).
- Example: `nmap -sV 10.10.10.10`

### OS Detection

```bash
nmap -O <target-ip>
```

- `-O`: Attempts to identify the operating system and its version based on TCP/IP stack characteristics.
- Requires root privileges.
- Example: `nmap -O 10.10.10.10`

### Aggressive Scan

```bash
nmap -A <target-ip>
```

- `-A`: Enables aggressive scanning (includes `-sV`, `-O`, script scanning, and traceroute).
- Comprehensive but noisy and resource-intensive.
- Example: `nmap -A 10.10.10.10`

## Useful Nmap Options

- **Port Specification**:
  - `-p <port>`: Scan specific ports (e.g., `-p 80,443`).
  - `-p-`: Scan all 65,535 ports (e.g., `-p-` for a full port scan).
  - `--top-ports <n>`: Scan the top `n` most common ports (e.g., `--top-ports 100`).
- **Timing Control**:
  - `-T<0-5>`: Set timing template (0 = slowest, 5 = fastest; e.g., `-T4` for faster scans).
  - `--min-rate <n>`: Minimum packets per second (e.g., `--min-rate 100`).
  - `--max-retries <n>`: Maximum retry attempts (e.g., `--max-retries 2`).
- **Output**:
  - `-oN <file>`: Save output in normal format (e.g., `-oN scan.txt`).
  - `-oX <file>`: Save output in XML format (e.g., `-oX scan.xml`).
  - `-oG <file>`: Save output in grepable format (e.g., `-oG scan.grep`).
  - `-oA <basename>`: Save output in all formats (normal, XML, grepable; e.g., `-oA scan`).
- **Stealth**:
  - `-sS`: Use SYN scan for stealthier operation.
  - `--randomize-hosts`: Randomize the order of scanned hosts in a range.
  - `-f`: Fragment packets to evade detection.
  - `--spoof-source <ip>`: Spoof the source IP (requires root and caution).
- **Script Scanning**:
  - `-sC`: Run default Nmap Scripting Engine (NSE) scripts.
  - `--script <script>`: Run specific NSE scripts (e.g., `--script http-enum`).
  - Example: `nmap --script vuln 10.10.10.10` to scan for vulnerabilities.

**Example with Multiple Options**:

```bash
nmap -sS -sV -p 80,443,8080 -T4 -oN scan.txt 10.10.10.10
```

- Performs a SYN scan, detects service versions, scans ports 80, 443, and 8080, uses aggressive timing, and saves output to `scan.txt`.

## Nmap Workflow

Follow this step-by-step process for effective network enumeration:

1. **Basic Host Discovery**:

   ```bash
   nmap -sn <target-ip/range>
   ```

   - `-sn`: Ping scan to discover live hosts without port scanning.
   - Example: `nmap -sn 10.10.10.0/24`

2. **Quick Port Scan**:

   ```bash
   nmap -sS <target-ip>
   ```

   - Identifies open ports using a stealth SYN scan.

3. **Service and Version Detection**:

   ```bash
   nmap -sV <target-ip>
   ```

   - Gathers service and version information for open ports.

4. **OS and Script Scanning**:

   ```bash
   nmap -sS -sV -O -sC <target-ip>
   ```

   - Combines SYN scan, version detection, OS detection, and default NSE scripts.

5. **Comprehensive Scan**:

   ```bash
   nmap -A -p- <target-ip>
   ```

   - Performs an aggressive scan on all ports for maximum information.

**Example Workflow**:

```bash
nmap -sn 10.10.10.0/24
nmap -sS 10.10.10.10
nmap -sV -sC 10.10.10.10
nmap -A -p 80,443,8080 10.10.10.10
```

## Best Practices

1. **Start with Stealth Scans**: Use `-sS` or `-sn` to minimize detection during initial scans.
2. **Limit Port Range**: Scan specific ports (e.g., `-p 80,443`) to reduce scan time and noise.
3. **Adjust Timing**: Use `-T4` for faster scans but reduce to `-T2` or lower if rate-limiting is detected.
4. **Save Output**: Always save results with `-oA` for later analysis or reporting.
5. **Use NSE Scripts Wisely**: Run targeted scripts (e.g., `--script http-enum`) to avoid unnecessary noise.
6. **Stay Legal**: Only scan networks or systems with explicit permission.
7. **Combine Tools**: Pair Nmap with tools like **Metasploit** or **Nikto** for deeper analysis.

## Common Issues and Solutions

- **Firewall Blocking**: Use `-Pn` to skip host discovery if firewalls block ping probes.
  - Example: `nmap -Pn -sS 10.10.10.10`
- **Slow Scans**: Increase speed with `-T4` or `--min-rate`, but monitor for dropped packets.
- **Incomplete Results**: Use `--reason` to show why ports are reported as open/closed/filtered.
- **Permission Errors**: Run Nmap as root (`sudo nmap`) for privileged scans like `-sS` or `-O`.

## Additional Notes

- **NSE Scripts**: Explore `/usr/share/nmap/scripts/` for additional scripts (e.g., `vuln`, `http-enum`, `smb-vuln*`).
- **Custom Scripts**: Write custom NSE scripts for specific needs (refer to Nmap documentation).
- **Verbose Output**: Use `-v` or `-vv` for detailed output during debugging.
- **Documentation**: Run `nmap --help` or visit the official Nmap website for advanced options.
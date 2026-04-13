# SSH Brute-Force Detection using Linux Logs

## Objective
Simulate and detect an SSH brute-force attack using system logs (`journalctl`) in a controlled lab environment.

------------------------------------------------------------------------------------------------------------------------------------

## Tools & Environment
- Kali Linux
- OpenSSH Server
- journalctl (systemd logs)
- Linux CLI

------------------------------------------------------------------------------------------------------------------------------------

## Attack Simulation

Multiple failed SSH login attempts were generated using invalid credentials:

ssh root@127.0.0.1
ssh kali@127.0.0.1

------------------------------------------------------------------------------------------------------------------------------------

## Log Analysis

1. Identify Failed Login Attempts
Command: journalctl -u ssh --no-pager | grep "Failed password"

2. Count Failed Attempts
Command: journalctl -u ssh | grep "Failed password" | wc -l

3. Extract Attacker IP Address
Command: journalctl -u ssh | grep "Failed password" | awk '{print $(NF-3)}' | sort | uniq -c

4. Identify Targeted Users
Command: journalctl -u ssh | grep "Failed password" | awk '{print $(NF-5)}' | sort | uniq -c

5. Detect Successful Login
Command: journalctl -u ssh | grep "Accepted password"

------------------------------------------------------------------------------------------------------------------------------------

## Findings

Attack Type: SSH Brute Force
Attacker IP: 127.0.0.1
Targeted Users: root, kali
Total Failed Attempts: 33
Successful Login Observed: Yes (user: kali)

------------------------------------------------------------------------------------------------------------------------------------

## SOC Analysis

The logs indicate repeated failed login attempts from a single IP address within a short time frame, followed by a successful login.

This behavior strongly suggests:  Potential account compromise after brute-force attack

------------------------------------------------------------------------------------------------------------------------------------

## Detection Logic

A brute-force attack can be identified using:
1. High number of failed login attempts
2. Same source IP
3. Short time interval
4. Followed by successful authentication

------------------------------------------------------------------------------------------------------------------------------------

## MITRE ATT&CK Mapping

T1110 — Brute Force
T1078 — Valid Accounts

------------------------------------------------------------------------------------------------------------------------------------

## Recommendations

Disable root login (PermitRootLogin no)
Implement account lockout policies
Enable multi-factor authentication (MFA)
Deploy fail2ban or similar tools
Monitor authentication logs in SIEM

------------------------------------------------------------------------------------------------------------------------------------

## 📸 Screenshots

### 🔹 SSH Login Attempts

![SSH Attempt](screenshots/1. ssh login attempt.png)

### 🔹 Extracting Failed Logins

![Failed Logs](screenshots/4. extracting only failed password logs.png)

### 🔹 Extracting IP & Count

![IP Extraction](screenshots/5. extracting failed password count and attacker ip address.png)

### 🔹 Successful Login Detection

![Accepted Login](screenshots/6. extracting accepted passwords and failed attempted login users.png)

------------------------------------------------------------------------------------------------------------------------------------

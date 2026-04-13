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

```bash
ssh root@127.0.0.1
ssh kali@127.0.0.1

Log Analysis
1. Identify Failed Login Attempts
journalctl -u ssh --no-pager | grep "Failed password"

2. Count Failed Attempts
journalctl -u ssh | grep "Failed password" | wc -l

3. Extract Attacker IP Address
journalctl -u ssh | grep "Failed password" | awk '{print $(NF-3)}' | sort | uniq -c

4. Identify Targeted Users
journalctl -u ssh | grep "Failed password" | awk '{print $(NF-5)}' | sort | uniq -c

5. Detect Successful Login
journalctl -u ssh | grep "Accepted password"

------------------------------------------------------------------------------------------------------------------------------------

## Findings

Findings
Attack Type: SSH Brute Force
Attacker IP: 127.0.0.1
Targeted Users: root, kali
Total Failed Attempts: 33
Successful Login Observed: Yes (user: kali)

------------------------------------------------------------------------------------------------------------------------------------

## SOC Analysis

The logs indicate repeated failed login attempts from a single IP address within a short time frame, followed by a successful login.

This behavior strongly suggests:  Potential account compromise after brute-force attack

Detection Logic

A brute-force attack can be identified using:
High number of failed login attempts
Same source IP
Short time interval
Followed by successful authentication

MITRE ATT&CK Mapping
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

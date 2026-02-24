# Apache-Web-Server-Log-Analysis-Project
Web servers record every client interaction inside log files. This project demonstrates how to deploy an Apache web server, generate realistic attack-like traffic, collect logs remotely, and analyze them using Linux command-line tools.
Objectives

Deploy an Apache web server

Generate suspicious web traffic

Collect Apache access logs remotely

Perform command-line log analysis

Document findings as a security case report

**Lab Environment**
Component	Role
Kali Linux	Web Server (Log Generation)
Kali Purple	Log Analysis Workstation
Network Mode	NAT
🛠 Tools & Technologies

Apache2

Bash Shell

curl

scp

awk, grep, wc, sort, uniq

nano

Setup & Configuration
1. Start Both Virtual Machines

Power on Kali Linux and Kali Purple.

2. Verify Network Connectivity

Find IP addresses:

ip a

Test communication:

ping <OTHER_VM_IP>
3. Install Apache on Kali Linux
sudo apt update
sudo apt install apache2 -y
sudo systemctl start apache2
sudo systemctl enable apache2

Verify:

sudo systemctl status apache2

Or open in browser:

http://localhost
4. Locate Apache Logs
ls /var/log/apache2/

Important files:

access.log – client requests

error.log – server problems

🚨 Simulating Suspicious Traffic

From Kali Purple:

for i in {1..70}; do curl http://<KALI_LINUX_IP>/login; done

This simulates:

Directory brute forcing

Automated reconnaissance

Scanning behavior

📥 Pull Logs for Analysis
scp kali@<KALI_LINUX_IP>:/var/log/apache2/access.log ~/apache_access.log

Confirm:

ls ~
🔎 Log Analysis
Count Total Requests
wc -l apache_access.log
Top IP Addresses
awk '{print $1}' apache_access.log | sort | uniq -c | sort -nr | head
Count 404 Errors
grep " 404 " apache_access.log | wc -l
Find Login Attempts
grep -i "login" apache_access.log
Check Time Clustering
grep -i "login" apache_access.log | head

High-frequency requests within seconds often indicate automation.

📝 Case Notes (Incident Report)
Date: 25-Feb-2026
Analyst: Wick
Server IP: 192.168.217.128
Log Source: Apache access.log

Observations:
- Total of 70 log entries analyzed.
- All 70 requests returned HTTP status code 404 (Not Found).
- Repeated requests targeting the /login endpoint observed.
- Requests originated primarily from a single IP address: 192.168.217.129.
- Requests occurred within a very short time window.
- All login-related requests used user-agent: curl/8.18.0.

Suspicious Activity:
- Automated brute-force or credential-stuffing style attack.
- High-frequency scripted behavior.

Severity:
High

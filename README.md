![Badge](https://img.shields.io/badge/web%20server-Apache-red)


This project demonstrates how to **analyze Apache web server logs to detect suspicious activity** using Linux command-line tools.

The lab simulates **automated scanning and brute-force style web requests** and investigates the resulting logs to identify abnormal behavior.

This project demonstrates practical **Security Operations Center (SOC) analyst skills**, including:

- Web server log analysis
- Detection of suspicious request patterns
- Identifying reconnaissance activity
- Writing investigation case notes

---

#  Project Objectives

This lab focuses on learning how to:

- Understand how **web server logs record user activity**
- Generate realistic web traffic and suspicious requests
- Extract logs from a server
- Analyze logs using Linux command-line tools
- Document investigation findings

---

# 🧪 Lab Environment

| Component | Purpose |
|----------|---------|
| <mark>Kali Linux VM<mark> | Web server generating logs |
| <mark>Kali Purple VM<mark> | Log analysis system |
| Apache Web Server | Generates web access logs |
| Linux CLI Tools | Log analysis |

Both systems run as **virtual machines** on the same NAT network.

---

#  Step 1 — Verify Network Connectivity

Find the IP address of both systems:

```bash
ip a
```

Test communication between the two machines:

```bash
ping <LINUX_IP>
```

Successful replies confirm that the lab systems can communicate.

---

#  Step 2 — Install Apache Web Server

On the **<mark>Kali Linux VM<mark>**:

Update package lists:

```bash
sudo apt update
```

Install Apache:

```bash
sudo apt install apache2 -y
```

Start the Apache web server:

```bash
sudo systemctl start apache2
sudo systemctl enable apache2
```

Verify Apache is running:

```bash
sudo systemctl status apache2
```

You can also test via browser:

```
http://localhost
```

---

#  Step 3 — Locate Apache Log Files

Apache automatically records activity in log files.

Check the log directory:

```bash
ls /var/log/apache2/
```

Important logs include:

| File | Purpose |
|-----|---------|
| access.log | Records all web requests |
| error.log | Records server errors |

The **access.log** file is the primary source for analyzing web traffic.

---

#  Step 4 — Generate Suspicious Traffic

To simulate malicious behavior, repeated requests are generated from the **<mark>Kali Purple analysis VM<mark>**.

Run the following command:

```bash
for i in {1..70}; do curl -s http://<KALI_LINUX_IP>/login > /dev/null; done
```
*> /dev/null* avoids terminal clutter

Explanation:

- The `/login` page does not exist.
- The server returns **HTTP 404 (Not Found)**.
- Repeated requests simulate **automated scanning or brute-force attempts**.

This creates realistic log entries in the Apache access logs.

---

#  Step 5 — Confirm Logs Were Generated

On the **<mark>Kali Purple<mark>**, view the access log:

```bash
less /var/log/apache2/access.log
```

You should see entries with **404 status codes**, confirming that requests were logged.

---

#  Step 6 — Transfer Logs for Analysis

From the **<mark>Kali Purple VM<mark>**, retrieve the logs:

```bash
scp kali@<KALI_LINUX_IP>:/var/log/apache2/access.log ~/apache_access.log
```

Verify the file was transferred:

```bash
ls ~
```

You should see:

```
apache_access.log
```

---

#  Step 7 — Begin Log Analysis

# In <mark>Kali Purple VM<mark>

### Count Total Requests

```bash
wc -l apache_access.log
```

This shows the total number of recorded web requests.

---

### Identify Top Source IP Addresses

```bash
awk '{print $1}' apache_access.log | sort | uniq -c | sort -nr | head
```

This identifies the **most frequent visitors**.

---

### Count HTTP 404 Errors

```bash
grep " 404 " apache_access.log | wc -l
```

Large numbers of 404 responses often indicate:

- directory brute forcing
- automated scanning
- reconnaissance activity

---

### Identify Login-Related Requests

```bash
grep -i "login" apache_access.log
```

This helps detect **targeted attacks against login pages**.

---

### Check Request Timing Patterns

```bash
grep -i "login" apache_access.log | head
```

Requests occurring **within seconds of each other** may indicate automated scripts.

---

#  Step 8 — Investigation Case Notes

Example investigation notes:

```
Date: 25-Feb-2026
Analyst: <Name>

Server IP: 192.168.217.128
Log Source: Apache access.log
```

### Observations

- 70 log entries analyzed.
- All requests returned HTTP **404 Not Found**.
- Repeated requests targeted the `/login` endpoint.
- Requests originated primarily from IP address **192.168.217.129**.
- Requests occurred within seconds of each other.
- User-agent string observed: **curl/8.18.0**.

---

### Suspicious Activity Identified

The request pattern indicates possible:

- automated scanning
- directory brute forcing
- credential stuffing attempts

The repeated requests from a single host strongly suggest **scripted activity rather than normal user behavior**.

---

# ⚠️ Security Assessment

| Indicator | Finding |
|----------|---------|
| High request frequency | Detected |
| Repeated endpoint targeting | `/login` |
| HTTP response codes | 404 errors |
| Source IP concentration | Single host |
| User agent | curl automation |

Severity assessment:

```
High
```

---

#  Key Skills Demonstrated

This project demonstrates:

- Web server log investigation
- Suspicious traffic pattern detection
- Linux log analysis techniques
- Incident documentation
- Evidence-based investigation

---

|

# Apache Web Server Log Analysis Lab Project


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
**Kali Linux**
![Screenshot 2026-03-06 155244](https://github.com/user-attachments/assets/6d1ff7b2-8384-4678-a929-45b2e9f85146)

**Kali Purple**
![Screenshot 2026-03-06 155457](https://github.com/user-attachments/assets/c6a40b54-5a9d-43fd-a311-c2be2c1e7c92)



Test communication between the two machines:

```bash
ping <LINUX_IP>
```

Successful replies confirm that the lab systems can communicate.

---

#  Step 2 — Install Apache Web Server

On the **<mark>Kali Linux VM<mark>**:

Update & upgrade the package lists:

```bash
sudo apt update && sudo apt upgrade
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

![Screenshot 2026-03-06 163419](https://github.com/user-attachments/assets/33922a87-20ba-4d4c-808f-ae5826e92536)


You can also test via browser:

```
http://localhost
```

<img width="999" height="243" alt="image" src="https://github.com/user-attachments/assets/c6ffd9d5-33ec-4da0-bccb-c8b2a5cb7541" />


---

#  Step 3 — Locate Apache Log Files

Apache automatically records activity in log files.

Check the log directory:

```bash
ls /var/log/apache2/
```

<img width="858" height="63" alt="image" src="https://github.com/user-attachments/assets/29aec571-5648-4b44-8309-48c0f643679e" />


Important logs include:

| File | Purpose |
|-----|---------|
| access.log | Records all web requests |
| error.log | Records server errors |

The **access.log** file is the primary source for analyzing web traffic.

---

#  Step 4 — Generate Suspicious Traffic

To simulate malicious behavior, repeated requests are generated from the **<mark>Kali Purple VM<mark>**.

Run the following command:

```bash
for i in {1..70}; do curl -s http://<KALI_LINUX_IP>/login > /dev/null; done
```

<img width="640" height="67" alt="image" src="https://github.com/user-attachments/assets/2a6eaed6-c95a-4366-ba77-ea8cb8f67fc6" />


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

<img width="786" height="198" alt="image" src="https://github.com/user-attachments/assets/858d31b7-7d49-4450-8001-abb7f5321f22" />


---

#  Step 6 — Transfer Logs for Analysis

From the **<mark>Kali Purple VM<mark>**, retrieve the logs:

```bash
scp kali@<KALI_LINUX_IP>:/var/log/apache2/access.log ~/apache_access.log
```

<img width="620" height="127" alt="image" src="https://github.com/user-attachments/assets/86c4b97e-722c-4c53-aca7-dcce2eaac628" />


Verify the file was transferred:

```bash
ls ~
```

You should see:

```
apache_access.log
```

<img width="220" height="66" alt="image" src="https://github.com/user-attachments/assets/75b55061-bad0-4f53-85f9-9fb5f39942a8" />


---

#  Step 7 — Begin Log Analysis

# In <mark>Kali Purple VM<mark>

### Count Total Requests

```bash
wc -l apache_access.log
```
This shows the total number of recorded web requests.

<img width="226" height="57" alt="image" src="https://github.com/user-attachments/assets/00c68c5a-d105-467d-a6b5-ec8ed4aad2f0" />

---

### Identify Top Source IP Addresses

```bash
awk '{print $1}' apache_access.log | sort | uniq -c | sort -nr | head
```

This identifies the **most frequent visitors**.

<img width="594" height="88" alt="image" src="https://github.com/user-attachments/assets/bc72822e-c32b-4451-844a-f25ac974ebfd" />

---

### Count HTTP 404 Errors

```bash
grep " 404 " apache_access.log | wc -l
```

Large numbers of 404 responses often indicate:

- directory brute forcing
- automated scanning
- reconnaissance activity

<img width="351" height="79" alt="image" src="https://github.com/user-attachments/assets/0a3b49bf-6828-43a7-a1bc-3ddabc19ab8a" />


---

### Identify Login-Related Requests

```bash
grep -i "login" apache_access.log
```

This helps detect **targeted attacks against login pages**.

<img width="778" height="346" alt="image" src="https://github.com/user-attachments/assets/3833dcc4-638e-4941-8679-3064893cf46c" />

---

### Check Request Timing Patterns

```bash
grep -i "login" apache_access.log | head
```

Requests occurring **within seconds of each other** may indicate automated scripts.

<img width="771" height="196" alt="image" src="https://github.com/user-attachments/assets/22ea6e49-8d0c-49a1-b5b1-d8a09944b033" />


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

- 71 log entries analyzed.
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

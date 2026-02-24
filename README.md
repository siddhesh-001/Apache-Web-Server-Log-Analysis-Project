# Apache-Web-Server-Log-Analysis-Project
Web servers record every client interaction inside log files. This project demonstrates how to deploy an Apache web server, generate realistic attack-like traffic, collect logs remotely, and analyze them using Linux command-line tools.

**Objective**: Deploy an Apache web server, Generate suspicious web traffic, Collect Apache access logs remotely, Perform command-line log analysis, Document findings as a security case report

**Lab Environment**
| Component    | Role                        |
| ------------ | --------------------------- |
| Kali Linux   | Web Server (Log Generation) |
| Kali Purple  | Log Analysis Workstation    |
| Network Mode | NAT                         |

**Tools**

. Apache2


**Setup & Configuration**
**1.** Power on Kali Linux and Kali Purple.
**2.** Find IP addresses:
_ip a_

**Test communication:**
_ping <OTHER_VM_IP>_

**3.** Install Apache on Kali Linux
_sudo apt update
sudo apt install apache2 -y
sudo systemctl start apache2
sudo systemctl enable apache2_

**Verify:**
_sudo systemctl status apache2_

**4.** Locate Apache Logs: 
_ls /var/log/apache2/_

Important files:

access.log – client requests

error.log – server problems



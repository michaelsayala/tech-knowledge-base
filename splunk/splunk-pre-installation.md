# Splunk Installation and Pre-Configuration Guide (Linux)

## Overview
This guide provides a step-by-step process for preparing a Linux server and installing Splunk in a consistent and production-aligned way.

## 1. Pre-Installation Setup

### Install Required Packages
```
sudo su
yum install -y net-tools wget ntpstat
```
Purpose:
- net-tools: network validation (ports and connections)
- wget: download Splunk packages
- ntpstat: verify time synchronization

### Set Hostname
```
hostnamectl set-hostname <hostname>
```
### Create Splunk User and Directory
```
groupadd splunk
useradd -g splunk splunk

mkdir -p /opt/splunk
chown -R splunk:splunk /opt/splunk
```
> Notes:

Splunk should not run as root
/opt/splunk is the standard installation directory

## 2. Pre-Installation Checks
### System Validation
```
cat /etc/os-release
df -kh
cd /opt/splunk && df -kh
lscpu
free -h
```

### Validate Splunk User
```
id -u splunk
lslogins -u splunk
ls -ld /opt/splunk
```

### Check Required Ports
```
netstat -tuln | grep -E ':8000|:8089|:9997'
```

Default Ports:

8000: Splunk Web UI
8089: Management port (splunkd)
9997: Data ingestion (forwarding)

## 3. Disable Transparent Huge Pages (THP)
```
sudo vi /etc/systemd/system/disable-thp.service
```
```
[Unit]
Description=Disable THP
After=network.target

[Service]
Type=oneshot
ExecStart=/bin/bash -c "echo never > /sys/kernel/mm/transparent_hugepage/enabled"
ExecStart=/bin/bash -c "echo never > /sys/kernel/mm/transparent_hugepage/defrag"
RemainAfterExit=true

[Install]
WantedBy=multi-user.target
```
```
sudo systemctl daemon-reexec
sudo systemctl enable disable-thp
sudo systemctl start disable-thp
```
Check Current Status
```
cat /sys/kernel/mm/transparent_hugepage/enabled
cat /sys/kernel/mm/transparent_hugepage/defrag
```
Why this matters:
Transparent Huge Pages can cause performance degradation and latency issues. Splunk requires THP to be disabled for stable performance.

## 4. Configure System Limits (ulimits)
Check Current Limits
```
ulimit -a
```
Edit the limits configuration file:
```
vi /etc/security/limits.conf
```
Add the following lines at the end:
```
* soft nofile 64000
* hard nofile unlimited
* soft nproc 16000
* hard nproc unlimited
* soft data 8000000
* hard data unlimited
* soft fsize unlimited
* hard fsize unlimited
```

## 5. Time Synchronization (NTP)
Verify Time Sync
```
ntpstat
```
Why this matters:
Accurate time is critical for indexing, searching, and correlation across distributed Splunk environments.

## 6. Install Splunk

### Switch to Splunk User
```
sudo su - splunk
```
### Download Splunk
```
cd /home/splunk
wget <SPLUNK_DOWNLOAD_URL>
```
### Extract and Install
```
tar -xvzf splunk-*.tgz
sudo mv splunk /opt/
```
### Start Splunk (First Time)
```
/opt/splunk/bin/splunk start --accept-license
```
### Enable Splunk Boot Start
```
sudo su
/opt/splunk/bin/splunk enable boot-start -user splunk
```

## 7. Post-Installation Verification
Run as the Splunk user:
```
/opt/splunk/bin/splunk show servername
/opt/splunk/bin/splunk show default-hostname

/opt/splunk/bin/splunk show web-port
/opt/splunk/bin/splunk show splunkd-port
/opt/splunk/bin/splunk show kvstore-port
/opt/splunk/bin/splunk show appserver-ports

/opt/splunk/bin/splunk display boot-start
/opt/splunk/bin/splunk status
```

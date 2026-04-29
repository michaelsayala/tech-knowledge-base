# Splunk Installation Guide

## 1. Install Required Packages
```
sudo su
yum install -y net-tools ntpstat wget
```
## 2. Set Hostname
```
hostnamectl set-hostname <hostname>
```
## 3. Create Splunk User and Directory
```
groupadd splunk
useradd -g splunk splunk
mkdir -p /opt/splunk
chown -R splunk:splunk /opt/splunk
```
## 4. Disable Transparent Huge Pages (THP)
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
## 5. Increase System Limits (ulimit)
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
## 6. Reboot the Server
```
reboot
```
## 7. Verify Configuration
```
ulimit -a
or
cat /etc/security/limits.conf
```

Verify THP is Disabled
```
cat /sys/kernel/mm/transparent_hugepage/enabled
cat /sys/kernel/mm/transparent_hugepage/defrag
```
Expected output should include:
```
[never]
```
## 8. Enable Splunk Boot Start (Post-Installation)
```
sudo su
/opt/splunk/bin/splunk enable boot-start -user splunk
```

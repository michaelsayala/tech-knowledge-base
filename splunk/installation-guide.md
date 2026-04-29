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
cd /etc/init.d/
vi disable_thp
```
```
#!/bin/bash
### BEGIN INIT INFO
# Provides:          disable-thp
# Required-Start:    $local_fs
# Default-Start:     2 3 4 5
### END INIT INFO

case $1 in
 start)
   if [ -d /sys/kernel/mm/transparent_hugepage ]; then
     thp_path=/sys/kernel/mm/transparent_hugepage
   elif [ -d /sys/kernel/mm/redhat_transparent_hugepage ]; then
     thp_path=/sys/kernel/mm/redhat_transparent_hugepage
   else
     exit 0
   fi

   echo 'never' > ${thp_path}/enabled
   echo 'never' > ${thp_path}/defrag

   if [[ $(cat ${thp_path}/khugepaged/defrag) =~ ^[0-1]+$ ]]; then
     echo 0 > ${thp_path}/khugepaged/defrag
   else
     echo 'no' > ${thp_path}/khugepaged/defrag
   fi
   ;;
esac
```
```
chmod 755 disable_thp
chkconfig --add disable_thp
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

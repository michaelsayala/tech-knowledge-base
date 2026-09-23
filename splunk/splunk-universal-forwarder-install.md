# Splunk Universal Forwarder Installation and Configuration Guide (Linux)

## Overview

This guide provides a step-by-step process for installing and configuring the Splunk Universal Forwarder (UF) on a Linux server.

The Universal Forwarder is used to collect and forward machine data such as:

* Linux system logs
* Application logs
* Security logs
* Custom log files
* Directory-based log data

The examples in this guide use:

* Installation directory: `/opt/splunkforwarder`
* Service user: `splunkfwd`
* Receiving Indexer: `<INDEXER_IP>:9997`
* Deployment Server: `<DEPLOYMENT_SERVER_IP>:8089`

---

## 1. Pre-Installation Setup

### Install Required Packages

```bash
sudo su

yum install -y net-tools wget ntpstat
```

Purpose:

* `net-tools`: network validation
* `wget`: download Splunk packages
* `ntpstat`: verify time synchronization

### Set Hostname

```bash
hostnamectl set-hostname <hostname>
```

Verify:

```bash
hostname
hostnamectl
```

### Check Operating System

```bash
cat /etc/os-release
```

### Check System Resources

```bash
df -kh
lscpu
free -h
```

The Universal Forwarder should have sufficient disk space for the software and monitored data.

---

## 2. Create Splunk Forwarder User

Recent Splunk Universal Forwarder versions support a least-privileged `splunkfwd` user on supported Linux systems.

Check whether the user already exists:

```bash
id splunkfwd
```

If it does not exist:

```bash
groupadd splunkfwd
useradd -g splunkfwd splunkfwd
```

Verify:

```bash
id splunkfwd
```

> Notes:
>
> The Universal Forwarder should run as a non-root user where possible.
>
> When installing from a tar package, the `splunkfwd` user may need to be created manually.

---

## 3. Create Installation Directory

Create the Splunk directory:

```bash
mkdir -p /opt/splunkforwarder
```

Set ownership:

```bash
chown -R splunkfwd:splunkfwd /opt/splunkforwarder
```

Verify:

```bash
ls -ld /opt/splunkforwarder
```

Expected ownership:

```text
splunkfwd splunkfwd
```

---

## 4. Check Required Ports

The Universal Forwarder commonly communicates with Splunk components using the following ports:

| Port | Purpose                               |
| ---- | ------------------------------------- |
| 8089 | Splunk management / Deployment Server |
| 9997 | Splunk receiving port                 |
| 8088 | HTTP Event Collector, if used         |

Check existing listeners:

```bash
netstat -tuln
```

Or:

```bash
ss -tuln
```

Test connectivity to the receiving Indexer:

```bash
telnet <INDEXER_IP> 9997
```

Test connectivity to the Deployment Server:

```bash
telnet <DEPLOYMENT_SERVER_IP> 8089
```

---

## 5. Time Synchronization

Verify time synchronization:

```bash
ntpstat
```

Or:

```bash
timedatectl
```

Check:

```bash
timedatectl status
```

> Accurate system time is important for log timestamps, event correlation, and distributed Splunk environments.

---

## 6. Download the Universal Forwarder

Download the appropriate Linux Universal Forwarder package from Splunk.

Example:

```bash
cd /tmp

wget <SPLUNK_UF_DOWNLOAD_URL>
```

Verify the downloaded file:

```bash
ls -lh /tmp/splunkforwarder*
```

> Use the appropriate Universal Forwarder version for your environment.

---

## 7. Install the Universal Forwarder

### Tar Package

If using a `.tgz` package:

```bash
tar -xvzf splunkforwarder-*.tgz -C /opt
```

Verify:

```bash
ls -ld /opt/splunkforwarder
```

Set ownership:

```bash
chown -R splunkfwd:splunkfwd /opt/splunkforwarder
```

Verify:

```bash
ls -ld /opt/splunkforwarder
```

---

## 8. Start the Universal Forwarder

Switch to the Splunk user:

```bash
su - splunkfwd
```

Set the Splunk home directory:

```bash
export SPLUNK_HOME=/opt/splunkforwarder
```

Start Splunk:

```bash
$SPLUNK_HOME/bin/splunk start --accept-license
```

Check status:

```bash
$SPLUNK_HOME/bin/splunk status
```

---

## 9. Enable Boot Start

Switch to root:

```bash
sudo su
```

Enable the Universal Forwarder to start automatically:

```bash
/opt/splunkforwarder/bin/splunk enable boot-start
```

If required, specify the service user:

```bash
/opt/splunkforwarder/bin/splunk enable boot-start -user splunkfwd
```

Check the boot-start configuration:

```bash
/opt/splunkforwarder/bin/splunk display boot-start
```

> The exact boot-start configuration depends on the Linux distribution and Universal Forwarder version.

---

## 10. Configure Receiving Indexer

The Universal Forwarder needs a destination where it can send collected data.

Switch to the Splunk user:

```bash
su - splunkfwd
```

Set the Splunk home:

```bash
export SPLUNK_HOME=/opt/splunkforwarder
```

Add the receiving Indexer:

```bash
$SPLUNK_HOME/bin/splunk add forward-server <INDEXER_IP>:9997
```

Example:

```bash
$SPLUNK_HOME/bin/splunk add forward-server 172.16.10.101:9997
```

Restart the Universal Forwarder:

```bash
$SPLUNK_HOME/bin/splunk restart
```

Splunk stores forwarding configuration in:

```text
$SPLUNK_HOME/etc/system/local/outputs.conf
```

Verify:

```bash
cat $SPLUNK_HOME/etc/system/local/outputs.conf
```

---

## 11. Configure outputs.conf Manually

Alternatively, configure the forwarding destination directly.

Edit:

```bash
vi /opt/splunkforwarder/etc/system/local/outputs.conf
```

Example:

```ini
[tcpout]
defaultGroup = indexer_group

[tcpout:indexer_group]
server = 172.16.10.101:9997
```

For multiple Indexers:

```ini
[tcpout]
defaultGroup = indexer_group

[tcpout:indexer_group]
server = 172.16.10.101:9997,172.16.10.102:9997,172.16.10.103:9997
```

Restart:

```bash
/opt/splunkforwarder/bin/splunk restart
```

> `outputs.conf` controls where the Universal Forwarder sends collected data. `inputs.conf` controls what data the forwarder collects.

---

## 12. Configure Deployment Server

A Deployment Server can centrally manage Universal Forwarder configurations.

Configure the Deployment Server:

```bash
/opt/splunkforwarder/bin/splunk set deploy-poll <DEPLOYMENT_SERVER_IP>:8089
```

Example:

```bash
/opt/splunkforwarder/bin/splunk set deploy-poll 172.16.10.100:8089
```

Verify:

```bash
cat /opt/splunkforwarder/etc/system/local/deploymentclient.conf
```

Restart:

```bash
/opt/splunkforwarder/bin/splunk restart
```

The configuration is typically stored in:

```text
/opt/splunkforwarder/etc/system/local/deploymentclient.conf
```

---

## 13. Configure inputs.conf

`inputs.conf` controls the data collected by the Universal Forwarder.

Edit:

```bash
vi /opt/splunkforwarder/etc/system/local/inputs.conf
```

Example:

```ini
[monitor:///var/log]
disabled = false
index = os
sourcetype = linux:syslog
```

For a specific log file:

```ini
[monitor:///var/log/messages]
disabled = false
index = os
sourcetype = syslog
```

For an application log directory:

```ini
[monitor:///var/log/myapp]
disabled = false
index = application
sourcetype = myapp
recursive = true
```

Restart:

```bash
/opt/splunkforwarder/bin/splunk restart
```

> In larger environments, `inputs.conf` is commonly managed through a Deployment Server rather than manually on every Universal Forwarder.

---

## 14. Verify Forwarding Configuration

List configured forward servers:

```bash
/opt/splunkforwarder/bin/splunk list forward-server
```

Check the Universal Forwarder status:

```bash
/opt/splunkforwarder/bin/splunk status
```

Check the server name:

```bash
/opt/splunkforwarder/bin/splunk show servername
```

Check the hostname:

```bash
/opt/splunkforwarder/bin/splunk show default-hostname
```

---

## 15. Verify Deployment Server Connection

Check the deployment client configuration:

```bash
cat /opt/splunkforwarder/etc/system/local/deploymentclient.conf
```

Check the Deployment Client log:

```bash
tail -f /opt/splunkforwarder/var/log/splunk/splunkd.log
```

Search for Deployment Server activity:

```bash
grep -i "deployment" /opt/splunkforwarder/var/log/splunk/splunkd.log
```

---

## 16. Verify Data Collection

Check configured inputs:

```bash
/opt/splunkforwarder/bin/splunk list monitor
```

Example output should show monitored paths such as:

```text
/var/log/messages
/var/log
```

Check the internal Splunk logs:

```bash
tail -f /opt/splunkforwarder/var/log/splunk/splunkd.log
```

---

## 17. Verify Data in Splunk

From the Splunk Search Head, search for the Universal Forwarder's hostname:

```spl
index=os host="<hostname>"
```

For Linux syslog:

```spl
index=os sourcetype=syslog host="<hostname>"
```

For a specific application:

```spl
index=application host="<hostname>"
```

Check the Universal Forwarder host:

```spl
index=* host="<hostname>"
| stats count by index sourcetype
```

---

## 18. Check Universal Forwarder Logs

Important log locations:

```text
/opt/splunkforwarder/var/log/splunk/
```

Main Splunk daemon log:

```bash
tail -f /opt/splunkforwarder/var/log/splunk/splunkd.log
```

Search for errors:

```bash
grep -i "error" /opt/splunkforwarder/var/log/splunk/splunkd.log
```

Search for connection problems:

```bash
grep -Ei "connection|connect|refused|timeout" \
/opt/splunkforwarder/var/log/splunk/splunkd.log
```

---

## 19. Common Troubleshooting

### Universal Forwarder Is Not Running

Check:

```bash
/opt/splunkforwarder/bin/splunk status
```

Start:

```bash
/opt/splunkforwarder/bin/splunk start
```

---

### Cannot Connect to Indexer

Test port connectivity:

```bash
telnet <INDEXER_IP> 9997
```

Or:

```bash
nc -zv <INDEXER_IP> 9997
```

Check:

```bash
/opt/splunkforwarder/bin/splunk list forward-server
```

Review:

```bash
tail -f /opt/splunkforwarder/var/log/splunk/splunkd.log
```

---

### Cannot Connect to Deployment Server

Test:

```bash
telnet <DEPLOYMENT_SERVER_IP> 8089
```

Check:

```bash
cat /opt/splunkforwarder/etc/system/local/deploymentclient.conf
```

Review:

```bash
grep -i deployment \
/opt/splunkforwarder/var/log/splunk/splunkd.log
```

---

### Data Is Not Being Collected

Check configured monitors:

```bash
/opt/splunkforwarder/bin/splunk list monitor
```

Check permissions:

```bash
ls -l /var/log/messages
```

Check directory permissions:

```bash
ls -ld /var/log
```

If running as `splunkfwd`, make sure the account can read the required log files.

Check:

```bash
sudo -u splunkfwd cat /var/log/messages
```

---

### Configuration Changes Are Not Taking Effect

Check the configuration:

```bash
/opt/splunkforwarder/bin/splunk btool inputs list --debug
```

Check outputs:

```bash
/opt/splunkforwarder/bin/splunk btool outputs list --debug
```

Check deployment client:

```bash
/opt/splunkforwarder/bin/splunk btool deploymentclient list --debug
```

Restart:

```bash
/opt/splunkforwarder/bin/splunk restart
```

> `btool` is useful for determining which configuration files are contributing settings to the running configuration.

---

## 20. Final Verification Checklist

### Operating System

```bash
hostname
cat /etc/os-release
df -kh
free -h
lscpu
timedatectl
```

### Splunk User

```bash
id splunkfwd
ls -ld /opt/splunkforwarder
```

### Universal Forwarder

```bash
/opt/splunkforwarder/bin/splunk status
/opt/splunkforwarder/bin/splunk show servername
```

### Forwarding

```bash
/opt/splunkforwarder/bin/splunk list forward-server
```

### Inputs

```bash
/opt/splunkforwarder/bin/splunk list monitor
```

### Deployment Server

```bash
cat /opt/splunkforwarder/etc/system/local/deploymentclient.conf
```

### Configuration

```bash
/opt/splunkforwarder/bin/splunk btool inputs list --debug
/opt/splunkforwarder/bin/splunk btool outputs list --debug
```

### Logs

```bash
tail -f /opt/splunkforwarder/var/log/splunk/splunkd.log
```

---

## Reference Configuration

### outputs.conf

```ini
[tcpout]
defaultGroup = indexer_group

[tcpout:indexer_group]
server = <INDEXER_IP>:9997
```

### inputs.conf

```ini
[monitor:///var/log]
disabled = false
index = os
sourcetype = linux:syslog
```

### deploymentclient.conf

```ini
[target-broker:deploymentServer]
targetUri = <DEPLOYMENT_SERVER_IP>:8089

[deployment-client]
clientName = <HOSTNAME>
```

---

## Key Configuration Files

| File                    | Purpose                                           |
| ----------------------- | ------------------------------------------------- |
| `inputs.conf`           | Defines data collected by the Universal Forwarder |
| `outputs.conf`          | Defines where data is forwarded                   |
| `deploymentclient.conf` | Defines the Deployment Server connection          |
| `server.conf`           | Server and system-level configuration             |
| `user-seed.conf`        | Initial administrator credentials                 |
| `splunkd.log`           | Main Universal Forwarder log                      |

Configuration files are normally located under:

```text
/opt/splunkforwarder/etc/system/local/
```

---

## Important Ports

| Port | Purpose                               |
| ---- | ------------------------------------- |
| 8089 | Splunk management / Deployment Server |
| 9997 | Splunk-to-Splunk forwarding           |
| 8088 | HTTP Event Collector                  |

---

## References

* Splunk Universal Forwarder installation documentation
* Splunk Universal Forwarder configuration documentation
* Splunk `outputs.conf` documentation
* Splunk Deployment Server documentation

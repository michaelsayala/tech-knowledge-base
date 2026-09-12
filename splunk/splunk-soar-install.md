# Splunk SOAR 8.7.0 Installation on RHEL 9

This guide covers the basic steps to prepare a RHEL 9 server and install **Splunk SOAR 8.7.0** using the unprivileged installation package.

## 1. Prerequisites

Before starting, make sure you have:

* RHEL 9 server
* Root or sudo access
* Internet connectivity
* Sufficient disk space and memory
* A hostname configured for the server
* Splunk SOAR installation package or download access

The commands below use:

```text
Splunk SOAR version: 8.7.0
Operating System:    RHEL 9
SOAR user:           phantom
SOAR home:           /opt/phantom
Installer directory: /opt/splunk-soar
```

---

## 2. Check and Enable Firewalld

Check the current status of `firewalld`:

```bash
systemctl status firewalld
```

Start `firewalld`:

```bash
sudo systemctl start firewalld
```

Enable `firewalld` to start automatically after a reboot:

```bash
sudo systemctl enable firewalld
```

Verify the service:

```bash
sudo systemctl status firewalld
```

You should see:

```text
Active: active (running)
```

---

## 3. Update the Operating System

Clean the YUM cache:

```bash
sudo yum clean all
```

Update the system packages:

```bash
sudo yum update -y
```

If the update requires a reboot:

```bash
sudo reboot
```

After reconnecting to the server, continue with the installation.

---

## 4. Create the Splunk SOAR User

Create the dedicated `phantom` user:

```bash
sudo useradd phantom
```

Set a password for the user:

```bash
sudo passwd phantom
```

The `phantom` user will be used for the unprivileged Splunk SOAR installation.

---

## 5. Create the Splunk SOAR Home Directory

Create the SOAR home directory:

```bash
sudo mkdir -p /opt/phantom
```

Change ownership to the `phantom` user:

```bash
sudo chown -R phantom:phantom /opt/phantom
```

Verify the directory:

```bash
ls -ld /opt/phantom
```

The owner and group should be:

```text
phantom phantom
```

---

## 6. Download the Splunk SOAR Installation Package

Change to the `/tmp` directory:

```bash
cd /tmp
```

Download the Splunk SOAR 8.7.0 unprivileged package:

```bash
wget -O splunk_soar-unpriv-8.7.0.232-281d350a-el9-x86_64.tgz \
"https://download.splunk.com/products/splunk_soar-unpriv/releases/8.7.0/linux/splunk_soar-unpriv-8.7.0.232-281d350a-el9-x86_64.tgz"
```

Verify that the package was downloaded:

```bash
ls -lh /tmp/splunk_soar-unpriv-*.tgz
```

---

## 7. Extract the Installation Package

Extract the Splunk SOAR package into `/opt`:

```bash
sudo tar -xvzf splunk_soar-unpriv-8.7.0.232-281d350a-el9-x86_64.tgz -C /opt/
```

Check the contents of `/opt`:

```bash
ls -l /opt
```

You should see the Splunk SOAR installer directory:

```text
/opt/splunk-soar
```

Change to the installer directory:

```bash
cd /opt/splunk-soar
```

Verify the installation files:

```bash
ls -l
```

---

## 8. Run the Splunk SOAR System Preparation Script

The `soar-prepare-system` script prepares the RHEL system for Splunk SOAR.

> **Important:** Run this step as the **root user**. Do not switch to the `phantom` user for this step.

Verify that you are running as root:

```bash
whoami
```

Expected output:

```text
root
```

Change to the Splunk SOAR installer directory:

```bash
cd /opt/splunk-soar
```

Run the preparation script:

```bash
./soar-prepare-system --splunk-soar-home /opt/phantom
```

Allow the script to complete.

If the script reports missing dependencies or configuration requirements, resolve those issues before continuing.

---

## 9. Install Splunk SOAR

After the system preparation completes successfully, run the Splunk SOAR installer.

Change to the installer directory:

```bash
cd /opt/splunk-soar
```

Run the installer:

```bash
./soar-install --splunk-soar-home /opt/phantom
```

Follow the prompts displayed by the installer.

Allow the installation to complete before moving to the verification steps.

---

## 10. Verify the Installation

After the installation completes, verify the SOAR home directory:

```bash
ls -ld /opt/phantom
```

Check the contents:

```bash
ls -l /opt/phantom
```

You can also check for running SOAR processes:

```bash
ps -ef | grep -i phantom
```

Or:

```bash
ps -ef | grep -i soar
```

---

## 11. Verify Directory Permissions

Verify the ownership of the main directories:

```bash
ls -ld /opt/phantom
ls -ld /opt/splunk-soar
```

The `/opt/phantom` directory should be owned by the `phantom` user:

```text
phantom phantom
```

If necessary, correct the ownership:

```bash
sudo chown -R phantom:phantom /opt/phantom
```

---

# Quick Installation Summary

The following is a condensed version of the installation procedure.

```bash
# 1. Enable firewalld
sudo systemctl start firewalld
sudo systemctl enable firewalld

# 2. Update the operating system
sudo yum clean all
sudo yum update -y

# 3. Create the Splunk SOAR user
sudo useradd phantom
sudo passwd phantom

# 4. Create the SOAR home directory
sudo mkdir -p /opt/phantom
sudo chown -R phantom:phantom /opt/phantom

# 5. Download Splunk SOAR
cd /tmp

wget -O splunk_soar-unpriv-8.7.0.232-281d350a-el9-x86_64.tgz \
"https://download.splunk.com/products/splunk_soar-unpriv/releases/8.7.0/linux/splunk_soar-unpriv-8.7.0.232-281d350a-el9-x86_64.tgz"

# 6. Extract the package
sudo tar -xzf splunk_soar-unpriv-8.7.0.232-281d350a-el9-x86_64.tgz -C /opt

# 7. Go to the installer directory
cd /opt/splunk-soar

# 8. Prepare the system
# Run this as ROOT
./soar-prepare-system --splunk-soar-home /opt/phantom

# 9. Install Splunk SOAR
./soar-install --splunk-soar-home /opt/phantom
```

---

## Installation Directory Structure

After installation, the environment should use the following directories:

```text
/opt/
├── phantom/
│   └── Splunk SOAR application data
│
└── splunk-soar/
    └── Splunk SOAR installer
```

### Key Directories

| Directory          | Purpose                                                      |
| ------------------ | ------------------------------------------------------------ |
| `/opt/phantom`     | Splunk SOAR application/home directory                       |
| `/opt/splunk-soar` | Splunk SOAR installer directory                              |
| `/tmp`             | Temporary location used to download the installation package |

---

## Important Notes

### System Preparation

The following command must be run as **root** based on the tested installation procedure:

```bash
./soar-prepare-system --splunk-soar-home /opt/phantom
```

### SOAR User

The `phantom` user is created for the unprivileged Splunk SOAR installation:

```bash
sudo useradd phantom
```

### SOAR Home

The SOAR application directory is:

```text
/opt/phantom
```

### Installer

The installation files are located under:

```text
/opt/splunk-soar
```

### Troubleshooting

If the installation fails, check:

1. The system preparation script output.
2. File and directory permissions.
3. Available disk space.
4. Required RHEL packages and dependencies.
5. Network connectivity.
6. The Splunk SOAR installation logs.

Do not continue with the installation until `soar-prepare-system` completes successfully.

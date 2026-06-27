# RHEL 9 VirtualBox Template (Homelab)

A standardized **RHEL 9 base VM template** for Oracle VirtualBox, designed to quickly deploy consistent Linux systems for homelab environments.

---
## Overview

This template provides a reusable RHEL 9 base image that can be cloned to build multiple lab servers.

The goal is to eliminate repetitive OS installation and ensure consistency across all lab nodes.

---
## Base VM Specification

| Component | Configuration |
|----------|--------------|
| OS | RHEL 9 (Minimal Install) |
| CPU | 2 vCPU |
| Memory | 4 GB RAM |
| Disk | 40 GB (Dynamic) |
| Network | NAT + Host-Only Adapter |

---

## Prerequisites

- Oracle VirtualBox installed
- RHEL 9 ISO
- Internet connection for package updates

Download RHEL:
https://developers.redhat.com/
---

## VM Configuration

### Create Virtual Machine

- Name: `RHEL9-TEMPLATE`
- Type: Linux
- Version: Red Hat (64-bit)

### System Resources

- CPU: 2
- Memory: 4096 MB
- Disk: 40 GB (VDI, dynamically allocated)

### Network Setup

- Adapter 1: NAT (internet access)
- Adapter 2: Host-Only (lab communication)

---

## Installation Steps

### 1. Install RHEL

- Select **Minimal Install**
- Enable network connectivity
- Grant sudo privileges
---

## Post-Installation Configuration

### System Update
```
sudo dnf update -y
sudo reboot
```
---

### Install Required Packages
```
sudo dnf install -y \
wget curl vim net-tools bind-utils unzip tar \
tcpdump rsync git bash-completion chrony
```
---

### Enable Time Synchronization
```
sudo systemctl enable chronyd
sudo systemctl start chronyd
```
---

### Enable SSH Service
```
sudo systemctl enable sshd
sudo systemctl start sshd
```
---

## Template Cleanup (Before Cloning)

Run the following before taking a snapshot:

sudo journalctl --rotate
sudo journalctl --vacuum-time=1s

sudo rm -f /etc/ssh/ssh_host_*
sudo truncate -s 0 /etc/machine-id

---

## Snapshot

Create a baseline snapshot:

- Snapshot name: Fresh-RHEL9-Template

VirtualBox path:
Machine → Snapshots → Take Snapshot

---

## Cloning Strategy

Use Full Clone for each new VM.

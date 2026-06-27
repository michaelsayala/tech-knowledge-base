# SSH Key Setup (Linux / RHEL VM Access)

This document describes how to generate an SSH key pair and configure passwordless SSH access to a Linux (RHEL-based) virtual machine.

---

## 1. Generate SSH Key Pair (Local Machine)

```
ssh-keygen -t rsa -b 2048 -f directory_path_store/keys/splunk_ssh
```

### Output Files
- splunk_ssh — Private key (must be kept secure)
- splunk_ssh.pub — Public key (to be deployed on the VM)

---

## 2. Copy Public Key
```
cat directory_path_store/keys/splunk_ssh.pub
```
Copy the entire output of the public key.

---

## 3. Configure the VM for SSH Access

Log in to the VM and run the following commands:

```
mkdir -p ~/.ssh
chmod 700 ~/.ssh
```
---

## 4. Add Public Key

Open the authorized keys file:

vi ~/.ssh/authorized_keys

Paste the public key content and save the file.

---

## 5. Set Correct Permissions
```
chmod 600 ~/.ssh/authorized_keys
chown root:root ~/.ssh/authorized_keys
```
If using a non-root user:
```
chown <user>:<user> ~/.ssh/authorized_keys
```
---

## 6. Test SSH Connection
```
ssh -i directory_path_store/keys/splunk_ssh <user>@<vm-ip>
```
Example:
```
ssh -i directory_path_store/keys/splunk_ssh root@192.168.56.10
```

---

## Security Notes

- Keep private key (splunk_ssh) secure and never share it
- Ensure correct permissions:
  - ~/.ssh → 700
  - authorized_keys → 600
  - private key → 600 (local machine)
- Use separate keys per environment (recommended)

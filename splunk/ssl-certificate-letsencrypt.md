# SSL Certificate Setup for Splunk Web Using Cloudflare and Let's Encrypt

This guide explains how to obtain and deploy a trusted SSL/TLS certificate for Splunk Web running on Red Hat Enterprise Linux (RHEL) using Cloudflare DNS validation and Let's Encrypt.

---

# Prerequisites

Before proceeding, ensure the following requirements are met:

* A registered domain name
* DNS hosted in Cloudflare
* Administrative access to Cloudflare
* Root or sudo access on the Splunk servers
* RHEL-based operating system
* Splunk Enterprise installed

---

# Architecture Example

Domain:

michaelsayala.com

Subdomains:

* cm1.michaelsayala.com
* idx1.michaelsayala.com
* idx2.michaelsayala.com
* idx3.michaelsayala.com

Certificate:

* *.michaelsayala.com
* michaelsayala.com

---

# Step 1 - Install Certbot

Install Certbot from the RHEL repositories.

```bash
sudo dnf install -y certbot
```

If Certbot is unavailable, install the EPEL repository first.

```bash
sudo dnf install -y epel-release
sudo dnf install -y certbot
```

Verify the installation.

```bash
certbot --version
```

---

# Step 2 - Install the Cloudflare DNS Plugin

Install the Cloudflare DNS plugin for Certbot.

```bash
sudo dnf install -y python3-certbot-dns-cloudflare
```

Verify that Certbot recognizes the plugin.

```bash
certbot plugins
```

Expected output should include:

```text
dns-cloudflare
```

---

# Step 3 - Create a Cloudflare API Token

1. Log in to Cloudflare.

2. Navigate to:

   My Profile → API Tokens

3. Select **Create Token**.

4. Configure the following permissions:

| Permission Category | Permission |
| ------------------- | ---------- |
| Zone                | DNS Edit   |
| Zone                | Zone Read  |

5. Restrict the token to the required zone.
6. Copy the generated API token.

---

# Step 4 - Configure Cloudflare Credentials

Create the Certbot credentials directory.

```bash
sudo mkdir -p /root/.secrets/certbot
```

Create the Cloudflare credentials file.

```bash
sudo vi /root/.secrets/certbot/cloudflare.ini
```

Add the following content:

```ini
dns_cloudflare_api_token = YOUR_API_TOKEN
```

Protect the file by restricting access.

```bash
sudo chmod 600 /root/.secrets/certbot/cloudflare.ini
```

Verify permissions.

```bash
ls -l /root/.secrets/certbot/cloudflare.ini
```

Expected output:

```text
-rw------- 1 root root
```

---

# Step 5 - Request a Wildcard Certificate

Request a wildcard certificate for the Splunk environment.

```bash
sudo certbot certonly \
  --dns-cloudflare \
  --dns-cloudflare-credentials /root/.secrets/certbot/cloudflare.ini \
  -d "*.michaelsayala.com" \
  -d "michaelsayala.com"
```

Successful certificate generation will display a message similar to:

```text
Congratulations! Your certificate and chain have been saved.
```

---

# Step 6 - Verify Certificate Files

Verify that the certificate files were created.

```bash
ls -l /etc/letsencrypt/live/michaelsayala.com/
```

Expected files:

```text
cert.pem
chain.pem
fullchain.pem
privkey.pem
```

The following files will be used by Splunk:

* fullchain.pem
* privkey.pem

---

# Step 7 - Deploy Certificates to Splunk

Create a directory for custom certificates.

```bash
mkdir -p /opt/splunk/etc/auth/webcertificate/
```

Copy the certificate files.

```bash
cp /etc/letsencrypt/live/michaelsayala.com/fullchain.pem \
   /opt/splunk/etc/auth/webcertificate/

cp /etc/letsencrypt/live/michaelsayala.com/privkey.pem \
   /opt/splunk/etc/auth/webcertificate/
```

Apply appropriate permissions.

```bash
chown splunk:splunk /opt/splunk/etc/auth/webcertificate/*
chmod 600 /opt/splunk/etc/auth/webcertificate/*
```

Repeat on all Splunk instances that provide web access, such as:

* Cluster Manager
* Indexers (if Splunk Web is enabled)
* Search Heads

---

# Step 8 - Configure Splunk Web SSL

Edit the Splunk server configuration.

File location:

```text
$SPLUNK_HOME/etc/system/local/web.conf
```

Add or update the following settings:

```ini
[settings]
enableSplunkWebSSL = true
serverCert = /opt/splunk/etc/auth/webcertificate/fullchain.pem
privKeyPath = /opt/splunk/etc/auth/webcertificate/privkey.pem
```

Save the file.

---

# Step 9 - Restart Splunk

Restart the Splunk service.

```bash
/opt/splunk/bin/splunk restart
```

Verify that Splunk starts successfully and that SSL is enabled.

---

# Validation

Verify that Splunk Web port 8000 is serving the new certificate.

```bash
openssl s_client -connect localhost:8000 -showcerts
```

Verify certificate details.

```bash
openssl x509 -in /opt/splunk/etc/auth/webcertificate/fullchain.pem -text -noout
```

Check expiration date.

```bash
openssl x509 -in /opt/splunk/etc/auth/webcertificate/fullchain.pem -noout -dates
```

---


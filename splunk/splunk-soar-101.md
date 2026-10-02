# Splunk SOAR

> Foundational technical documentation covering Splunk SOAR purpose, components, architecture, usage, configuration, dependencies, and licensing.

---

# 1. Purpose

## 1.1 What is Splunk SOAR?

Splunk SOAR (Security Orchestration, Automation and Response) is a security automation and orchestration platform designed to help security teams **automate repetitive investigation and response tasks**.

SOAR can integrate security tools and use automated workflows to:

* Collect security data
* Enrich indicators
* Investigate suspicious activity
* Make decisions based on investigation results
* Execute response actions
* Record investigation results
* Reduce manual SOC processes

A simplified workflow is:

```text
Security Event
      │
      ▼
   Artifact
      │
      ▼
Investigation
      │
      ├──► Reputation
      ├──► WHOIS
      ├──► DNS
      ├──► Threat Intelligence
      └──► Other Security Tools
      │
      ▼
   Decision
      │
      ▼
 Automated Response
```

---

## 1.2 What Problems Does SOAR Solve?

Security analysts often perform the same investigation steps repeatedly.

For example, an analyst investigating a suspicious IP might manually:

```text
Copy IP
   │
   ▼
Check VirusTotal
   │
   ▼
Check WHOIS
   │
   ▼
Check DNS
   │
   ▼
Check Threat Intelligence
   │
   ▼
Review Results
   │
   ▼
Decide What To Do
```

SOAR can automate this process:

```text
Suspicious IP
      │
      ▼
SOAR Playbook
      │
      ├──► VirusTotal
      ├──► WHOIS
      ├──► DNS
      └──► Threat Intelligence
             │
             ▼
        Analyze Results
             │
             ▼
           Decision
             │
        ┌────┴────┐
        ▼         ▼
      Malicious  Benign
        │         │
        ▼         ▼
     Respond     Close
```

---

## 1.3 SOAR in a SOC

SOAR is commonly positioned between security alerts and response actions.

```text
                    SOC Architecture

 SIEM / Security Tools
          │
          ▼
       Alert
          │
          ▼
      Splunk SOAR
          │
     ┌────┴────┐
     │         │
     ▼         ▼
Investigation Automation
     │
     ▼
Decision / Analysis
     │
     ▼
Response
```

For a Splunk environment, a common architecture is:

```text
Splunk Enterprise / ES
          │
          │ Alert
          ▼
     Splunk SOAR
          │
    ┌─────┼─────────────┐
    ▼     ▼             ▼
VirusTotal  WHOIS      DNS
    │
    ▼
Firewall / EDR / Email / Other Security Tools
```

---

# 2. Components

The main concepts you need to understand in Splunk SOAR are:

```text
Splunk SOAR
│
├── Events
├── Artifacts
├── Containers
├── Playbooks
├── Apps
├── Actions
├── Assets
├── Vault
├── Custom Lists
├── Custom Functions
├── Data Paths
├── Prompts
└── Mission Control Integration
```

---

# 2.1 Containers

A **container** represents a security event or investigation.

A container can contain:

* Event information
* Artifacts
* Tags
* Labels
* Notes
* CEF fields
* Investigation information

Example:

```text
Container
│
├── Name: Suspicious Email Investigation
│
├── Description
│
├── Tags
│
├── Artifacts
│   ├── Email Address
│   ├── IP Address
│   ├── URL
│   └── Domain
│
└── Playbook
```

Think of a container as the **case/investigation record**.

---

# 2.2 Artifacts

Artifacts are individual pieces of information contained within an event.

Examples:

```text
IP Address
Domain
URL
Email Address
File Hash
Username
Hostname
```

Example:

```text
Container
│
└── Phishing Email
       │
       ├── IP: 94.154.43.192
       ├── URL: http://example.com/login
       ├── Domain: example.com
       └── Email: attacker@example.com
```

Artifacts are often the inputs used by playbooks.

---

# 2.3 CEF

Common Event Format (CEF) fields provide structured information about an artifact.

For example:

```text
artifact
│
├── cef
│   ├── requestURL
│   ├── sourceAddress
│   ├── destinationAddress
│   └── fileHash
│
└── cef_types
```

Example:

```text
cef.requestURL
    ↓
http://example.com/login
```

A playbook can reference these fields as inputs.

---

# 2.4 Playbooks

A playbook is an automated workflow.

Example:

```text
START
  │
  ▼
Get IP Artifact
  │
  ▼
IP Reputation
  │
  ▼
Analyze Result
  │
  ▼
Decision
 ┌┴─────────────┐
 ▼              ▼
Malicious      Benign
 │              │
 ▼              ▼
Block IP       Close
```

Playbooks are one of the most important concepts in SOAR.

---

# 2.5 Actions

Actions are operations performed by SOAR apps.

Examples:

```text
WHOIS
DNS Lookup
URL Reputation
IP Reputation
Block IP
Disable User
Send Email
Search SIEM
Create Ticket
Query Endpoint
```

A playbook uses actions to interact with external systems.

---

# 2.6 Apps

Apps provide integrations between SOAR and external products/services.

Examples:

```text
Splunk SOAR
│
├── Splunk
├── VirusTotal
├── WHOIS
├── DNS
├── Microsoft 365
├── Firewall
├── EDR
└── Ticketing System
```

An app provides actions that playbooks can call.

---

# 2.7 Assets

An asset represents a configured instance of an app/service.

For example:

```text
App:
Splunk

Asset:
splunk01
```

Another example:

```text
App:
VirusTotal

Asset:
virustotal_prod
```

The asset contains the connection and authentication configuration required for SOAR to communicate with the external service.

---

# 2.8 Custom Functions

Custom Functions allow developers to implement custom logic that is not provided directly by an app or standard playbook block.

Example:

```text
Input
 │
 ▼
Custom Function
 │
 ├── Parse
 ├── Transform
 ├── Calculate
 └── Normalize
 │
 ▼
Output
```

They are useful for custom data processing.

---

# 2.9 Custom Lists

Custom Lists store reusable values.

Examples:

```text
Known Malicious IPs
Known Trusted Domains
VIP Users
Blocked Countries
Approved Domains
```

Example:

```text
Trusted Domains

company.com
partner.com
vendor.com
```

A playbook can compare an artifact against a custom list.

---

# 2.10 Vault

The SOAR Vault provides secure storage for files associated with investigations.

Examples:

* Email attachments
* Malware samples
* Documents
* Suspicious files

A playbook can retrieve and process files stored in the Vault.

---

# 3. Architecture

## 3.1 Basic Architecture

A basic SOAR deployment can be represented as:

```text
             Security Events
                    │
                    ▼
             ┌─────────────┐
             │ Splunk SOAR │
             └──────┬──────┘
                    │
          ┌─────────┼──────────┐
          ▼         ▼          ▼
       SIEM       Threat      Security
                  Intel        Tools
          │         │          │
          └─────────┼──────────┘
                    ▼
                Playbooks
                    │
                    ▼
                 Actions
                    │
                    ▼
                Response
```

---

# 3.2 SOAR and Splunk Enterprise

A common architecture is:

```text
                         SOC Analyst
                              │
                              ▼
                      Splunk Enterprise
                              │
                              │ Alert
                              ▼
                         Splunk SOAR
                              │
                    ┌─────────┼─────────┐
                    ▼         ▼         ▼
                 SIEM       Threat     Security
                            Intel       Tools
                    │         │         │
                    └─────────┼─────────┘
                              ▼
                         Investigation
                              │
                              ▼
                            Decision
                              │
                     ┌────────┴────────┐
                     ▼                 ▼
                  Response          Closure
```

Splunk Enterprise can detect an event while SOAR performs automated investigation and response.

---

# 3.3 Email Phishing Architecture

A practical phishing investigation architecture might look like:

```text
                       Email
                         │
                         ▼
                    Email Server
                         │
                         ▼
                    Splunk SOAR
                         │
                 Extract Artifacts
                         │
        ┌────────────────┼─────────────────┐
        ▼                ▼                 ▼
       URL              IP              Domain
        │                │                 │
        ▼                ▼                 ▼
   URL Reputation   IP Reputation       WHOIS
        │                │                 │
        └────────────────┼─────────────────┘
                         ▼
                   Analyze Results
                         │
                         ▼
                       Decision
                         │
              ┌──────────┴──────────┐
              ▼                     ▼
          Malicious                Benign
              │                     │
              ▼                     ▼
         Response               Close Case
```

---

# 3.4 Playbook Execution Model

A playbook can be thought of as:

```text
Trigger
   │
   ▼
Input
   │
   ▼
Action
   │
   ▼
Action Result
   │
   ▼
Decision
   │
   ▼
Next Action
   │
   ▼
Response
```

For example:

```text
START
 │
 ▼
Get URL
 │
 ▼
URL Reputation
 │
 ▼
Read Result
 │
 ▼
Is malicious?
 │
 ├── YES ──► Block URL
 │
 └── NO ───► Continue Investigation
```

---

# 4. Usage

## 4.1 Common SOAR Use Cases

SOAR can automate:

* Phishing investigation
* Malicious IP investigation
* Malicious URL investigation
* Malware investigation
* Account compromise investigation
* Threat intelligence enrichment
* IOC enrichment
* Firewall blocking
* Endpoint isolation
* User account actions
* Ticket creation
* Notification workflows

---

# 4.2 IP Investigation

Example:

```text
IP Artifact
     │
     ▼
IP Reputation
     │
     ▼
WHOIS
     │
     ▼
DNS
     │
     ▼
Threat Intelligence
     │
     ▼
Analyze
```

Potential result:

```text
IP: 94.154.43.192

Reputation:
Malicious

WHOIS:
Provider information

DNS:
Associated domains

Decision:
Investigate / Block / Escalate
```

---

# 4.3 URL Investigation

Example:

```text
URL
 │
 ├──► URL Reputation
 │
 ├──► Domain Reputation
 │
 ├──► WHOIS
 │
 ├──► DNS
 │
 └──► URL Analysis
```

---

# 4.4 Phishing Investigation

A phishing workflow may inspect:

### Email sender

```text
From
Reply-To
Return-Path
```

### Authentication

```text
SPF
DKIM
DMARC
ARC
```

### URLs

```text
Domain
URL reputation
Redirects
Domain age
```

### Attachments

```text
File name
File type
Hash
Malware reputation
```

### Infrastructure

```text
Source IP
WHOIS
ASN
DNS
Geolocation
Threat intelligence
```

---

# 4.5 Playbook Results

Actions return results that can be used by subsequent playbook blocks.

Conceptually:

```text
Action
 │
 ▼
Action Result
 │
 ├── status
 ├── message
 ├── data
 └── summary
```

Example:

```text
URL Reputation
│
└── data
    ├── malicious
    ├── suspicious
    ├── harmless
    └── undetected
```

The playbook can use these values to make decisions.

---

# 4.6 Decision Logic

Example:

```text
Reputation Result
       │
       ▼
malicious > 0?
       │
  ┌────┴────┐
 YES        NO
  │          │
  ▼          ▼
Escalate   Continue
```

More advanced logic might combine multiple signals:

```text
SPF = fail
       +
DMARC = fail
       +
URL = malicious
       +
Domain = suspicious
       │
       ▼
High-Risk Investigation
```

---

# 4.7 Analyst Interaction

SOAR does not have to automate everything.

A playbook can pause for analyst input.

Example:

```text
Automated Investigation
          │
          ▼
      Risk Analysis
          │
          ▼
   Analyst Decision
      │         │
      ▼         ▼
   Approve    Reject
      │         │
      ▼         ▼
   Block      Close
```

This is useful for high-impact response actions.

---

# 5. Configuration

## 5.1 SOAR Configuration Areas

Important configuration areas include:

```text
Splunk SOAR
│
├── Administration
├── Users
├── Roles
├── Apps
├── Assets
├── Playbooks
├── Vault
├── Custom Lists
├── Custom Functions
├── System Settings
└── Authentication
```

---

# 5.2 Users

SOAR users can be assigned roles and permissions.

Example:

```text
User
 │
 ▼
Role
 │
 ├── View
 ├── Investigate
 ├── Execute
 └── Administer
```

Access should follow the principle of least privilege.

---

# 5.3 Roles

Roles determine what users can do within SOAR.

Examples of responsibilities may include:

```text
SOC Analyst
SOAR Developer
SOAR Administrator
Security Administrator
```

A user should only receive the permissions required for their responsibilities.

---

# 5.4 Apps

Apps must be installed before their integrations can be used.

Example:

```text
Install App
     │
     ▼
Configure Asset
     │
     ▼
Enter Credentials
     │
     ▼
Test Connectivity
     │
     ▼
Use Actions
```

---

# 5.5 Assets

An asset contains the connection details for an external service.

Example:

```text
Asset: virustotal_prod

API Key
Server
Port
Authentication
Configuration
```

A playbook then calls:

```text
VirusTotal
     │
     ▼
virustotal_prod
     │
     ▼
Action
```

---

# 5.6 Playbook Configuration

A playbook normally contains:

```text
START
  │
  ▼
Input
  │
  ▼
Action
  │
  ▼
Decision
  │
  ▼
Action
  │
  ▼
End
```

---

# 5.7 Data Paths

Data paths allow playbook blocks to reference values generated by previous actions.

Conceptually:

```text
Action A
   │
   └── data.result
          │
          ▼
      Action B Input
```

For example:

```text
URL Reputation
     │
     └── action_result.data
                    │
                    ▼
               Decision
```

Understanding data paths is critical when developing SOAR playbooks.

---

# 5.8 Prompts

Prompts can request information from an analyst during playbook execution.

Example:

```text
Is this IP confirmed malicious?

[ YES ] [ NO ]
```

The result can control the next branch of the playbook.

---

# 5.9 Email / IMAP Integration

SOAR can integrate with email systems for automated email investigation.

Conceptually:

```text
Mailbox
   │
   ▼
SOAR Email Integration
   │
   ▼
Email Event
   │
   ▼
Extract Artifacts
   │
   ├── IP
   ├── URL
   ├── Domain
   ├── Email
   └── Attachment
```

This can be used to build phishing-analysis workflows.

---

# 5.10 Splunk Integration

SOAR can integrate with Splunk Enterprise.

Example:

```text
Splunk Enterprise
       │
       │ Alert / Search
       ▼
   Splunk SOAR
       │
       ▼
 Playbook
       │
       ▼
Investigation / Response
```

SOAR can also interact with Splunk to retrieve or submit information depending on the configured integration and actions.

---

# 6. Dependencies

## 6.1 Operating System

Splunk SOAR deployments depend on supported operating-system and platform requirements.

For self-managed deployments, the operating system must meet the requirements of the specific SOAR release.

Always verify the supported operating system against the exact SOAR version being installed.

---

# 6.2 CPU and Memory

SOAR workload depends on:

* Number of events
* Number of playbooks
* Number of actions
* Number of concurrent playbooks
* Number of integrations
* Number of users
* Investigation complexity

More automation generally means more processing requirements.

---

# 6.3 Storage

Storage is required for:

* SOAR application data
* Containers
* Artifacts
* Playbook data
* Application data
* Logs
* Vault files
* Database data

Storage planning becomes especially important when handling email attachments or malware samples.

---

# 6.4 Network

SOAR requires connectivity to the systems it integrates with.

Example:

```text
                    SOAR
                      │
       ┌──────────────┼──────────────┐
       ▼              ▼              ▼
     Splunk        VirusTotal       DNS
       │              │              │
       ▼              ▼              ▼
    SIEM/API       API/HTTPS       DNS
```

Network requirements depend on the installed apps.

---

# 6.5 DNS

DNS is important for integrations.

SOAR may need to resolve:

```text
Splunk hostname
Threat intelligence APIs
Email servers
DNS services
Firewall management systems
Ticketing systems
```

Incorrect DNS can cause app connectivity failures.

---

# 6.6 TLS / Certificates

SOAR integrations frequently use HTTPS/TLS.

Examples:

```text
SOAR ──HTTPS──► Splunk
SOAR ──HTTPS──► Threat Intelligence
SOAR ──HTTPS──► Ticketing System
SOAR ──HTTPS──► Firewall API
```

Certificate validation problems can prevent an asset from connecting.

---

# 6.7 API Credentials

Many SOAR integrations require credentials.

Examples:

```text
API Key
Username / Password
OAuth Token
Client ID
Client Secret
Certificate
```

Credentials should be stored securely and rotated according to organizational policy.

---

# 6.8 External Security Tools

SOAR's usefulness depends heavily on the integrations available to it.

Examples:

```text
SIEM
EDR
Firewall
Email
Threat Intelligence
DNS
WHOIS
Ticketing
Identity Provider
Cloud Platforms
```

A playbook may fail if an external service is unavailable.

---

# 6.9 Splunk Enterprise Dependency

When SOAR is integrated with Splunk Enterprise, the environment may depend on:

```text
Splunk Enterprise
       │
       ├── Network connectivity
       ├── REST/API access
       ├── Authentication
       ├── TLS
       └── Appropriate permissions
```

The integration should be tested independently before building complex playbooks.

---

# 7. License / Cost

## 7.1 Splunk SOAR Licensing

SOAR licensing depends on the specific Splunk SOAR edition, deployment model, and commercial agreement.

Organizations should verify current licensing terms directly with Splunk for production deployments.

---

# 7.2 Community / Free Edition

Splunk SOAR may be available in a free Community Edition for learning and development.

A Community Edition environment can be useful for:

* Learning SOAR
* Building playbooks
* Testing apps
* Learning actions
* Developing custom functions
* Creating a home lab
* Understanding automation workflows

The exact limits depend on the edition/version.

For example, an installation may display license information similar to:

```text
License:
Free Community Edition

Seat Count:
Unlimited

Action Count:
0 / 100

Event Usage:
Unlimited

Tenant Count:
0 / 1
```

These values are license entitlements for that particular environment and should not be assumed to represent every SOAR edition.

---

# 7.3 Production Licensing

Production environments should be evaluated based on:

* Number of users
* Number of events
* Automation volume
* Number of actions
* Required integrations
* Deployment model
* High-availability requirements
* Support requirements
* Splunk commercial agreement

---

# 7.4 Cost Considerations

SOAR cost is not limited to the software license.

Consider:

```text
Total SOAR Cost
│
├── SOAR License
├── Compute
├── Storage
├── Network
├── Threat Intelligence Services
├── Security Tool Licenses
├── API Services
├── Support
├── Development
└── Administration
```

External integrations may have their own costs.

For example:

```text
SOAR
 │
 ├── VirusTotal ──► API subscription
 │
 ├── EDR ─────────► EDR license
 │
 ├── Firewall ────► Firewall license
 │
 └── Ticketing ───► Service license
```

---

# 7.5 Cost Optimization

Automation can reduce the amount of manual analyst work required for repetitive investigations.

Example:

```text
Without SOAR

Alert
 │
 ▼
Analyst
 │
 ├── Copy IP
 ├── Check reputation
 ├── Check WHOIS
 ├── Check DNS
 ├── Review results
 └── Document findings


With SOAR

Alert
 │
 ▼
SOAR Playbook
 │
 ├── Reputation
 ├── WHOIS
 ├── DNS
 ├── Analysis
 └── Documentation
 │
 ▼
Analyst reviews results
```

The goal is not necessarily to automate every decision.

A common approach is:

```text
Automate repetitive work
          +
Keep high-impact decisions under appropriate human control
```

---

# Quick Reference

## Core SOAR Concepts

| Component       | Purpose                               |
| --------------- | ------------------------------------- |
| Container       | Investigation/case                    |
| Artifact        | Individual piece of data              |
| CEF             | Structured artifact fields            |
| Playbook        | Automated workflow                    |
| Action          | Operation performed by an app         |
| App             | Integration package                   |
| Asset           | Configured instance of an integration |
| Custom Function | Custom processing logic               |
| Custom List     | Reusable list of values               |
| Vault           | Secure file storage                   |
| Prompt          | Requests analyst input                |

---

## Common SOAR Workflow

```text
EVENT
  │
  ▼
CONTAINER
  │
  ▼
ARTIFACTS
  │
  ▼
PLAYBOOK
  │
  ▼
ACTIONS
  │
  ▼
ACTION RESULTS
  │
  ▼
DECISION
  │
  ├───────────────┐
  ▼               ▼
INVESTIGATE      RESPOND
  │               │
  └───────┬───────┘
          ▼
        CLOSE
```

---

# Splunk SOAR Mental Model

The easiest way to understand Splunk SOAR is:

```text
                 SPLUNK SOAR
                      │
                      ▼
                   EVENT
                      │
                      ▼
                 ARTIFACTS
                      │
                      ▼
                  PLAYBOOK
                      │
             ┌────────┼────────┐
             ▼        ▼        ▼
           ACTION   ACTION   ACTION
             │        │        │
             └────────┼────────┘
                      ▼
                ACTION RESULTS
                      │
                      ▼
                   ANALYSIS
                      │
                      ▼
                   DECISION
                      │
              ┌───────┴───────┐
              ▼               ▼
           RESPONSE          CLOSE
```

The fundamental SOAR workflow is:

```text
COLLECT
   ↓
ENRICH
   ↓
INVESTIGATE
   ↓
ANALYZE
   ↓
DECIDE
   ↓
RESPOND
   ↓
DOCUMENT
```

The key distinction to remember is:

> **Splunk Enterprise primarily provides the data collection, indexing, searching, and analytics platform, while Splunk SOAR focuses on orchestrating security investigations and automating response actions.**

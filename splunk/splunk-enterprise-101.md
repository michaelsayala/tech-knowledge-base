# Splunk Enterprise

> Technical documentation covering the purpose, core components, architecture, usage, configuration, dependencies, and licensing considerations of Splunk Enterprise.

---

# 1. Purpose

## 1.1 What is Splunk Enterprise?

Splunk Enterprise is a data platform used to **collect, index, search, analyze, monitor, and visualize machine-generated data**.

It can ingest data from many different sources, including:

* Operating systems
* Servers
* Applications
* Databases
* Network devices
* Firewalls
* Security appliances
* Cloud platforms
* Authentication systems
* Containers
* APIs
* Custom applications

Splunk converts this machine-generated data into searchable events that can be investigated using **Search Processing Language (SPL)**.

A simplified data flow is:

```text
Data Sources
     │
     ▼
Data Collection
     │
     ▼
Parsing / Processing
     │
     ▼
Indexing
     │
     ▼
Search
     │
     ▼
Analysis / Visualization / Alerting
```

---

## 1.2 Problems Splunk Enterprise Solves

Splunk is commonly used to solve several operational and security problems.

### Centralized Log Management

Instead of administrators logging into individual servers:

```text
Server 01 ─┐
Server 02 ─┤
Server 03 ─┤
Firewall ───┤
AWS ────────┼──► Splunk
Database ───┤
Application ┘
```

Data can be centralized into Splunk and searched from a single platform.

---

### Security Monitoring

Splunk can be used to analyze:

* Authentication events
* Failed logins
* Privilege changes
* Malware indicators
* Network connections
* Firewall events
* Endpoint activity
* DNS activity
* Web activity
* Cloud activity

Example:

```spl
index=auth action=failure
| stats count by user, src_ip
| sort - count
```

This can help identify accounts experiencing unusually high numbers of failed authentication attempts.

---

### Operational Monitoring

Splunk can also monitor infrastructure and applications.

Examples:

* CPU errors
* Application failures
* HTTP errors
* Database errors
* Service failures
* Network issues
* Infrastructure events

Example:

```spl
index=application status>=500
| stats count by host
| sort - count
```

---

### Troubleshooting

Splunk allows administrators to correlate events from multiple systems.

For example:

```text
User reports application failure
             │
             ▼
Application logs
             │
             ├──► Web server logs
             │
             ├──► Database logs
             │
             ├──► Authentication logs
             │
             └──► Network logs
```

Instead of investigating each system independently, related events can be searched together.

---

## 1.3 Security Operations

Splunk Enterprise can serve as a foundation for a Security Operations Center.

Typical security workflow:

```text
Security Events
      │
      ▼
Data Ingestion
      │
      ▼
Splunk Indexers
      │
      ▼
SPL Detection
      │
      ▼
Alert
      │
      ▼
Investigation
      │
      ▼
Response
```

Splunk Enterprise can also be extended with products such as:

* Splunk Enterprise Security
* Splunk SOAR
* Splunk IT Service Intelligence
* Splunk Observability Cloud

---

## 1.4 Common Use Cases

| Use Case                  | Description                                  |
| ------------------------- | -------------------------------------------- |
| Log Management            | Centralize and search machine-generated data |
| SIEM                      | Security monitoring and investigation        |
| Threat Detection          | Detect suspicious activity                   |
| Incident Investigation    | Investigate security events                  |
| Infrastructure Monitoring | Monitor servers and systems                  |
| Application Monitoring    | Analyze application logs                     |
| Troubleshooting           | Investigate failures and errors              |
| Compliance                | Search and retain security/audit data        |
| Reporting                 | Generate operational and security reports    |
| Alerting                  | Notify teams when conditions occur           |
| Dashboards                | Visualize operational/security information   |
| Data Analytics            | Analyze large volumes of machine data        |

---

# 2. Components

Splunk Enterprise can be deployed as a single instance or as a distributed architecture.

The major components are:

```text
                         Splunk Enterprise
                                │
       ┌────────────────────────┼────────────────────────┐
       │                        │                        │
       ▼                        ▼                        ▼
 Forwarders                Indexers                Search Heads
       │                        │                        │
       │                        │                        │
       └────────────────────────┼────────────────────────┘
                                │
                    Management Components
                                │
       ┌───────────────┬────────┼────────┬──────────────┐
       ▼               ▼        ▼        ▼              ▼
Deployment Server  Cluster   License   Monitoring   Deployer
                   Manager   Manager    Console
```

---

## 2.1 Search Head

The Search Head provides the interface used to search and analyze data.

Primary responsibilities include:

* Executing searches
* Distributing searches to indexers
* Managing knowledge objects
* Dashboards
* Reports
* Alerts
* Scheduled searches
* Search jobs
* User interaction through Splunk Web

The Search Head generally **does not store the primary event data** in a distributed architecture.

Example:

```text
User
 │
 ▼
Search Head
 │
 ├──────────► Indexer 01
 ├──────────► Indexer 02
 └──────────► Indexer 03
                    │
                    ▼
              Search Results
```

---

## 2.2 Indexer

The Indexer is responsible for storing and searching indexed data.

Major responsibilities:

* Receive incoming data
* Parse data
* Index events
* Store buckets
* Execute searches
* Return search results

Example:

```text
Forwarder
    │
    ▼
Indexer
    │
    ├── Hot
    ├── Warm
    ├── Cold
    └── Frozen
```

Indexers are generally the most important components for **data storage and search capacity**.

---

## 2.3 Universal Forwarder

The Universal Forwarder (UF) is a lightweight Splunk agent used primarily for collecting and forwarding data.

Typical responsibilities:

* Monitor files
* Collect Windows Event Logs
* Collect Linux logs
* Forward data
* Perform limited input-side processing

Example:

```text
Linux Server
     │
     ├── /var/log/messages
     ├── /var/log/secure
     └── /var/log/application.log
              │
              ▼
       Universal Forwarder
              │
              ▼
           Indexer
```

The Universal Forwarder is commonly deployed on servers where Splunk needs to collect local log files.

---

## 2.4 Heavy Forwarder

A Heavy Forwarder is a full Splunk Enterprise instance configured primarily for data forwarding and processing.

Compared with a Universal Forwarder, it provides significantly more processing capabilities.

Common uses:

* Data routing
* Parsing
* Event filtering
* Data transformation
* Protocol handling
* Syslog collection
* Modular inputs
* API integrations
* Data masking

Example:

```text
Data Sources
     │
     ▼
Heavy Forwarder
     │
     ├── Security Data ─────► Security Indexers
     │
     ├── Application Data ──► Application Indexers
     │
     └── Other Data ────────► Other Destination
```

---

## 2.5 Cluster Manager

The Cluster Manager manages an **Indexer Cluster**.

Responsibilities include:

* Cluster configuration
* Peer management
* Replication configuration
* Search factor
* Replication factor
* Cluster health
* Bucket management
* Cluster coordination

Example:

```text
                 Cluster Manager
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
      Indexer 01   Indexer 02   Indexer 03
```

The Cluster Manager coordinates the indexer cluster but does not normally serve as the primary data-search node.

---

## 2.6 Search Head Cluster

A Search Head Cluster (SHC) consists of multiple Search Heads working together.

Example:

```text
             Search Head Cluster
          ┌────────┬────────┬────────┐
          │        │        │        │
          ▼        ▼        ▼        ▼
        SH01     SH02     SH03     SH04
          │        │        │        │
          └────────┴────────┴────────┘
                       │
                       ▼
                  Indexer Cluster
```

Benefits include:

* Search Head redundancy
* Higher availability
* Distributed user access
* Replication of knowledge objects
* Scheduled search coordination

---

## 2.7 Deployer

The Deployer is used to distribute applications and configuration packages to Search Head Cluster members.

Example:

```text
                 Deployer
                    │
          ┌─────────┼─────────┐
          ▼         ▼         ▼
        SH01      SH02      SH03
```

The Deployer is primarily associated with **Search Head Cluster configuration management**.

---

## 2.8 Deployment Server

The Deployment Server distributes configurations and apps to Splunk deployment clients.

Example:

```text
                Deployment Server
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
        UF01         UF02         UF03
```

Typical use:

* Deploy `inputs.conf`
* Deploy `outputs.conf`
* Deploy apps
* Configure forwarders
* Manage groups of Universal Forwarders

---

## 2.9 License Manager

The License Manager controls Splunk license allocation and usage.

It is responsible for:

* License pools
* License usage
* License violations
* License distribution

Example:

```text
             License Manager
                    │
          ┌─────────┼─────────┐
          ▼         ▼         ▼
       Indexer01 Indexer02 Indexer03
```

---

## 2.10 Monitoring Console

The Monitoring Console provides centralized monitoring for Splunk infrastructure.

It can monitor:

* Indexers
* Search Heads
* Search performance
* Indexing performance
* Forwarders
* Resource usage
* Cluster health
* License usage
* Distributed search

---

## 2.11 Knowledge Objects

Knowledge objects allow Splunk users and administrators to define reusable search-related configurations.

Examples:

* Saved searches
* Reports
* Alerts
* Dashboards
* Lookups
* Event types
* Tags
* Macros
* Calculated fields
* Field aliases
* Workflow actions

---

## 2.12 Apps and Add-ons

### App

An app generally provides a user-facing collection of dashboards, searches, reports, configurations, and other functionality.

### Add-on

An add-on generally provides data collection, parsing, field extraction, CIM mappings, or integration functionality.

Example:

```text
Splunk
 │
 ├── Splunk Enterprise Security
 │
 ├── Splunk App for AWS
 │
 ├── Splunk Add-on for AWS
 │
 └── Splunk Add-on for Microsoft Windows
```

---

# 3. Architecture

## 3.1 Single-Instance Architecture

The simplest Splunk deployment consists of a single Splunk Enterprise instance.

```text
              ┌─────────────────────┐
              │  Splunk Enterprise  │
              │                     │
              │ Search Head         │
              │ Indexer             │
              │ Splunk Web          │
              │ Management          │
              └──────────┬──────────┘
                         │
                    Data Sources
```

This architecture is useful for:

* Labs
* Development
* Testing
* Small environments

It is generally not the architecture chosen for large enterprise deployments requiring high availability and horizontal scaling.

---

# 3.2 Distributed Architecture

A distributed Splunk environment separates responsibilities across multiple servers.

```text
                       Users
                         │
                         ▼
                ┌─────────────────┐
                │   Search Heads  │
                │                 │
                │ SH01 SH02 SH03  │
                └────────┬────────┘
                         │
                         ▼
                ┌─────────────────┐
                │    Indexers     │
                │                 │
                │ IDX01 IDX02 IDX03
                └────────┬────────┘
                         ▲
                         │
                ┌────────┴────────┐
                │   Forwarders    │
                │                 │
                │ UF / HF         │
                └────────┬────────┘
                         ▲
                         │
                Data Sources
```

---

# 3.3 Indexer Cluster

Indexer clustering provides data redundancy and search availability.

Important concepts:

### Replication Factor

The Replication Factor (RF) specifies how many copies of indexed data should exist within the cluster.

Example:

```text
RF = 3

Event
 │
 ├──► Indexer 01
 ├──► Indexer 02
 └──► Indexer 03
```

There are three copies of the data.

---

### Search Factor

The Search Factor (SF) determines how many searchable copies of data are maintained.

Example:

```text
RF = 3
SF = 2
```

This means the cluster maintains three replicated copies while ensuring two copies are searchable.

---

# 3.4 Search Head Cluster

A Search Head Cluster provides redundancy for search functionality.

```text
                SHC
       ┌────────┼────────┐
       ▼        ▼        ▼
     SH01     SH02     SH03
       │        │        │
       └────────┼────────┘
                │
                ▼
         Indexer Cluster
```

Search Head Cluster members coordinate:

* Searches
* Scheduled searches
* Knowledge objects
* User sessions
* Configuration

---

# 3.5 Complete Enterprise Architecture

A larger enterprise deployment may look like:

```text
                              Users
                                │
                                ▼
                     ┌────────────────────┐
                     │   Search Head      │
                     │      Cluster       │
                     │                    │
                     │ SH01 SH02 SH03     │
                     └─────────┬──────────┘
                               │
                               ▼
                  ┌──────────────────────────┐
                  │      Indexer Cluster     │
                  │                          │
                  │ IDX01 IDX02 IDX03 IDX04 │
                  │ IDX05 IDX06 ...         │
                  └────────────▲─────────────┘
                               │
                        Data Ingestion
                               │
                ┌──────────────┴──────────────┐
                │                             │
          Universal Forwarders         Heavy Forwarders
                │                             │
                └──────────────┬──────────────┘
                               │
                         Data Sources


Management Plane:

     ┌────────────────┐
     │ Cluster Manager│
     └────────────────┘

     ┌────────────────┐
     │ Deployment     │
     │ Server         │
     └────────────────┘

     ┌────────────────┐
     │ Deployer       │
     └────────────────┘

     ┌────────────────┐
     │ License Manager│
     └────────────────┘

     ┌────────────────┐
     │ Monitoring     │
     │ Console        │
     └────────────────┘
```

---

# 4. Usage

## 4.1 Splunk Web

Splunk Web provides the graphical interface for Splunk.

Common areas include:

```text
Splunk Web
│
├── Search & Reporting
├── Dashboards
├── Alerts
├── Reports
├── Apps
├── Settings
├── Monitoring Console
└── User Management
```

The default Splunk Web port is commonly:

```text
8000/TCP
```

---

# 4.2 Searching

Splunk searches are written using SPL.

Basic search:

```spl
index=main
```

Search by host:

```spl
index=main host=server01
```

Search by sourcetype:

```spl
index=main sourcetype=linux_secure
```

Search for errors:

```spl
index=main error
```

---

# 4.3 Statistics

Example:

```spl
index=web
| stats count by status
```

Result:

```text
status    count
------    -----
200       15423
404        234
500         51
```

---

# 4.4 Time-Based Analysis

Example:

```spl
index=web
| timechart span=1h count
```

This produces an hourly event count.

---

# 4.5 Dashboards

Dashboards combine searches and visualizations.

Example:

```text
+------------------------------------------------+
|             Security Dashboard                 |
+----------------------+-------------------------+
| Total Events         | Failed Logins           |
|       1.2M           |        542              |
+----------------------+-------------------------+
| Events Over Time                              |
|                                              |
|              Graph                           |
|                                              |
+----------------------------------------------+
| Top Source IPs                                |
+----------------------------------------------+
```

---

# 4.6 Alerts

Alerts execute searches based on defined conditions.

Example:

```spl
index=auth action=failure
| stats count by user
| where count > 10
```

The alert could notify security personnel when the threshold is reached.

---

# 4.7 Reports

Reports are saved searches designed to provide recurring information.

Examples:

* Daily authentication report
* Weekly security report
* Top failed logins
* Application error report
* Data ingestion report

Reports can be scheduled.

---

# 4.8 Knowledge Objects

Common knowledge objects include:

```text
Knowledge Objects
│
├── Saved Searches
├── Reports
├── Alerts
├── Dashboards
├── Macros
├── Lookups
├── Event Types
├── Tags
├── Field Aliases
└── Calculated Fields
```

---

# 4.9 Data Models

Data models provide structured representations of events.

They are particularly important for applications such as Splunk Enterprise Security.

Example conceptual model:

```text
Authentication
│
├── User
├── Source
├── Destination
├── Action
└── Authentication Method
```

---

# 5. Configuration

Splunk configuration is primarily controlled through configuration files.

Most configuration files are located under:

```text
$SPLUNK_HOME/etc/
```

For a standard installation:

```text
/opt/splunk/etc/
```

---

# 5.1 Configuration File Structure

Important configuration files include:

| File                    | Purpose                          |
| ----------------------- | -------------------------------- |
| `server.conf`           | Core Splunk server configuration |
| `indexes.conf`          | Index definitions                |
| `inputs.conf`           | Data inputs                      |
| `outputs.conf`          | Data forwarding                  |
| `props.conf`            | Parsing and field configuration  |
| `transforms.conf`       | Transformations and routing      |
| `limits.conf`           | Search and system limits         |
| `authorize.conf`        | Roles and capabilities           |
| `authentication.conf`   | Authentication configuration     |
| `web.conf`              | Splunk Web configuration         |
| `distsearch.conf`       | Distributed search               |
| `deploymentclient.conf` | Deployment client configuration  |
| `app.conf`              | App metadata/configuration       |

---

# 5.2 inputs.conf

`inputs.conf` defines data inputs.

Example file monitoring:

```ini
[monitor:///var/log/application.log]
disabled = false
index = application
sourcetype = application:log
```

This tells Splunk to monitor:

```text
/var/log/application.log
```

and send the events to:

```text
index = application
```

with:

```text
sourcetype = application:log
```

---

# 5.3 outputs.conf

`outputs.conf` defines where data should be forwarded.

Example:

```ini
[tcpout]
defaultGroup = indexers

[tcpout:indexers]
server = idx01:9997,idx02:9997,idx03:9997
```

The common Splunk receiving port is:

```text
9997/TCP
```

---

# 5.4 indexes.conf

`indexes.conf` defines indexes.

Example:

```ini
[security]
homePath = $SPLUNK_DB/security/db
coldPath = $SPLUNK_DB/security/colddb
thawedPath = $SPLUNK_DB/security/thaweddb
```

An index provides logical and physical organization for stored data.

---

# 5.5 props.conf

`props.conf` controls various parsing and search-time behaviors.

Examples include:

* Sourcetype configuration
* Timestamp recognition
* Line breaking
* Field extractions
* Lookups
* Search-time configurations

---

# 5.6 transforms.conf

`transforms.conf` is commonly used for:

* Field extraction
* Event routing
* Data masking
* Lookup definitions
* Transform-based processing

It is frequently used together with `props.conf`.

---

# 5.7 Configuration Precedence

Splunk configuration can exist at multiple levels.

Common hierarchy:

```text
System Default
      │
      ▼
System Local
      │
      ▼
App Default
      │
      ▼
App Local
```

Generally, more specific configurations override less specific configurations.

This is important when troubleshooting configuration conflicts.

---

# 5.8 Splunk CLI

The Splunk CLI can be used for administration.

Examples:

Check status:

```bash
$SPLUNK_HOME/bin/splunk status
```

Start:

```bash
$SPLUNK_HOME/bin/splunk start
```

Stop:

```bash
$SPLUNK_HOME/bin/splunk stop
```

Restart:

```bash
$SPLUNK_HOME/bin/splunk restart
```

Check version:

```bash
$SPLUNK_HOME/bin/splunk version
```

Login:

```bash
$SPLUNK_HOME/bin/splunk login
```

---

# 5.9 REST API

Splunk provides a REST API for programmatic administration and integration.

Example conceptual request:

```text
Client
   │
   ▼
Splunk REST API
   │
   ▼
Splunk Enterprise
```

The management port is commonly:

```text
8089/TCP
```

REST APIs can be used for:

* Creating searches
* Managing configurations
* Managing users
* Managing indexes
* Managing apps
* Automation
* Integration with external systems

---

# 5.10 Configuration Management

In larger environments, configuration should not be manually changed on every server.

Common management methods include:

```text
Deployment Server
        │
        ▼
Universal Forwarders
```

and:

```text
Deployer
   │
   ▼
Search Head Cluster
```

and:

```text
Cluster Manager
   │
   ▼
Indexer Cluster
```

This creates a more controlled configuration-management model.

---

# 6. Dependencies

Splunk Enterprise depends on several infrastructure components.

---

# 6.1 Operating System

Splunk Enterprise requires a supported operating system.

Common enterprise environments use:

* Linux
* Windows

For Linux deployments, administrators commonly use distributions such as:

* Red Hat Enterprise Linux
* Rocky Linux
* AlmaLinux

Exact supported operating-system versions should always be checked against the Splunk version being deployed.

---

# 6.2 CPU

CPU requirements depend heavily on workload.

CPU requirements increase with:

* Higher ingestion rates
* More searches
* More concurrent users
* Complex SPL
* Data model acceleration
* Scheduled searches
* Correlation searches

---

# 6.3 Memory

RAM is important for:

* Search execution
* Concurrent searches
* Indexing
* Knowledge objects
* Data model acceleration
* Splunk services

Insufficient memory can cause poor search performance and system instability.

---

# 6.4 Storage

Storage is one of the most important Splunk dependencies.

Splunk requires storage for:

```text
Hot Data
   │
   ▼
Warm Data
   │
   ▼
Cold Data
   │
   ▼
Frozen Data
```

Storage requirements depend on:

```text
Daily Ingestion
        ×
Retention Period
        ×
Replication
        +
System Overhead
```

High ingestion environments require careful storage planning.

---

# 6.5 DNS

DNS is strongly recommended for distributed Splunk deployments.

Example:

```text
sh01.company.local
sh02.company.local

idx01.company.local
idx02.company.local
idx03.company.local

cm01.company.local
ds01.company.local
```

Poor DNS configuration can cause:

* Connection failures
* Cluster problems
* Certificate problems
* Distributed search failures

---

# 6.6 Network

Splunk requires network connectivity between components.

Common ports include:

|          Port | Purpose                      |
| ------------: | ---------------------------- |
|    `8000/TCP` | Splunk Web                   |
|    `8089/TCP` | Splunk management / REST API |
|    `9997/TCP` | Splunk receiving             |
|    `8088/TCP` | HTTP Event Collector         |
|    `8191/TCP` | KV Store                     |
| `514/TCP/UDP` | Common syslog port           |
|  `53/TCP/UDP` | DNS                          |
|     `123/UDP` | NTP                          |

The exact ports used depend on the deployment and configuration.

---

# 6.7 NTP / Time Synchronization

Time synchronization is important because Splunk heavily depends on timestamps.

Systems should maintain consistent time using NTP or an equivalent time synchronization mechanism.

Example:

```text
          NTP Source
              │
       ┌──────┼──────┐
       ▼      ▼      ▼
     SH01   IDX01   UF01
```

Incorrect system time can cause:

* Incorrect event timestamps
* Search-window problems
* Authentication issues
* Certificate problems
* Cluster coordination problems

---

# 6.8 TLS / Certificates

Secure Splunk environments commonly use TLS for communication.

Examples:

```text
Forwarder ──TLS──► Indexer
Search Head ──TLS──► Indexer
User ──HTTPS──► Splunk Web
```

Certificates may be used for:

* Splunk Web
* Management communication
* Forwarder-to-indexer communication
* Cluster communication
* Authentication integrations

---

# 6.9 Identity Providers

Enterprise environments may integrate Splunk with:

* LDAP
* Active Directory
* SAML
* SSO providers

Example:

```text
User
 │
 ▼
SSO / Identity Provider
 │
 ▼
Splunk
 │
 ▼
Splunk Role
```

This allows centralized identity management and role-based access control.

---

# 6.10 External Data Sources

Splunk can depend on external systems for data.

Examples:

```text
AWS
Azure
Google Cloud
Firewalls
Network Devices
Windows
Linux
Databases
Applications
SaaS Platforms
```

The availability and configuration of those systems directly affect data ingestion.

---

# 6.11 Splunk Apps and Add-ons

Apps and add-ons can introduce additional dependencies.

For example:

```text
Splunk
 │
 └── Splunk Add-on for AWS
       │
       └── AWS APIs / Services
```

If the external dependency changes, the Splunk integration may stop working.

---

# 7. License / Cost

## 7.1 Why Splunk Licensing Matters

Splunk licensing is an important part of architecture planning because licensing can affect:

* How much data can be ingested
* Which capabilities are available
* Infrastructure cost
* Data retention strategy
* Number of Splunk instances
* Deployment architecture

The exact commercial model and pricing can vary by Splunk offering, contract, deployment model, and licensing agreement.

---

# 7.2 Ingest-Based Licensing

Historically, Splunk Enterprise licensing has commonly been associated with the amount of data being ingested.

Conceptually:

```text
Data Sources
     │
     ▼
Daily Ingestion
     │
     ▼
License Consumption
```

For example:

```text
100 GB/day
     │
     ▼
Daily Licensed Volume
```

A larger ingestion volume generally requires a larger licensing entitlement under an ingest-based model.

---

# 7.3 Workload-Based Licensing

Splunk also offers workload-oriented licensing models for certain deployments and products.

Workload licensing focuses more on the compute/search workload rather than simply the volume of data ingested.

Conceptually:

```text
Data
 │
 ▼
Splunk Workload
 │
 ├── Search
 ├── Analytics
 └── Processing
```

The applicable model depends on the specific Splunk product and commercial agreement.

---

# 7.4 License Manager

A Splunk deployment can use a License Manager to manage license allocation.

Example:

```text
              License Manager
                    │
          ┌─────────┼─────────┐
          ▼         ▼         ▼
       Indexer01 Indexer02 Indexer03
```

The License Manager tracks license usage and communicates license information to participating Splunk instances.

---

# 7.5 License Pools

License pools can be used to allocate licensed capacity to groups of Splunk instances.

Conceptually:

```text
             License Pool
             500 GB/day
                  │
       ┌──────────┼──────────┐
       ▼          ▼          ▼
    Indexer01  Indexer02  Indexer03
```

This allows administrators to organize license capacity.

---

# 7.6 License Usage

Administrators should monitor license consumption.

Useful information includes:

* Current usage
* Daily usage
* Peak usage
* License quota
* Violations
* License pool usage

A sudden increase can indicate:

```text
New Data Source
       │
       ▼
Unexpected Ingestion Increase
       │
       ▼
Higher License Consumption
```

---

# 7.7 License Violations

A deployment can encounter license violations when licensed limits are exceeded, depending on the applicable licensing model and terms.

Administrators should investigate:

```text
High License Usage
        │
        ├──► New Data Source?
        │
        ├──► Duplicate Data?
        │
        ├──► Debug Logging?
        │
        ├──► Unexpected Application?
        │
        └──► Misconfigured Forwarder?
```

The solution is not always simply to increase the license.

Data volume should first be investigated.

---

# 7.8 Controlling Data Volume

Good data management can reduce unnecessary ingestion.

Potential approaches include:

* Remove unnecessary logs
* Reduce excessive debug logging
* Filter unwanted events
* Avoid duplicate ingestion
* Correct overly broad monitoring inputs
* Use appropriate sourcetypes
* Route data appropriately
* Review retention requirements

Example:

```text
100 GB/day
   │
   ├── 20 GB unnecessary debug data
   │
   └── 80 GB useful security/operational data
```

Reducing unnecessary data can improve both operational efficiency and licensing efficiency where the applicable license is volume-based.

---

# 7.9 Cost Components

The total cost of a Splunk deployment can involve more than the software license.

A practical cost model is:

```text
Total Cost
│
├── Splunk Licensing
│
├── Compute
│
├── Memory
│
├── Storage
│
├── Network
│
├── Cloud Infrastructure
│
├── Operating System
│
├── Support
│
├── Professional Services
│
└── Administration / Operations
```

For an on-premises deployment, hardware and storage can represent a significant part of the overall infrastructure cost.

For a cloud deployment, costs may include:

* Virtual machines
* Storage
* Network transfer
* Backup
* Monitoring
* Cloud service dependencies

---

# 7.10 Cost Planning Example

A simplified planning model:

```text
Daily Ingestion
      │
      ▼
Retention Requirement
      │
      ▼
Storage Requirement
      │
      ▼
Indexer Count
      │
      ▼
CPU / RAM Requirements
      │
      ▼
Infrastructure Cost
      │
      +
Splunk License
      │
      ▼
Total Deployment Cost
```

Before deploying Splunk at scale, determine:

1. Expected daily ingestion
2. Peak ingestion
3. Data sources
4. Retention requirements
5. Search workload
6. Number of users
7. Concurrent searches
8. High-availability requirements
9. Disaster-recovery requirements
10. Applicable Splunk licensing model

---

# Quick Reference

## Core Components

| Component           | Primary Responsibility              |
| ------------------- | ----------------------------------- |
| Search Head         | Search, dashboards, reports, alerts |
| Indexer             | Index and store data                |
| Universal Forwarder | Collect and forward data            |
| Heavy Forwarder     | Process and forward data            |
| Cluster Manager     | Manage indexer cluster              |
| Search Head Cluster | Provide Search Head HA/scaling      |
| Deployer            | Manage SHC configuration            |
| Deployment Server   | Manage deployment clients           |
| License Manager     | Manage license allocation           |
| Monitoring Console  | Monitor Splunk infrastructure       |

---

## Important Ports

|   Port | Function              |
| -----: | --------------------- |
| `8000` | Splunk Web            |
| `8089` | Management / REST API |
| `9997` | Splunk receiving      |
| `8088` | HTTP Event Collector  |
| `8191` | KV Store              |
|  `514` | Common syslog         |
|   `53` | DNS                   |
|  `123` | NTP                   |

---

## Important Configuration Files

```text
$SPLUNK_HOME/etc/

├── system/
│
├── apps/
│
└── deployment-apps/
```

Common configuration files:

```text
inputs.conf
outputs.conf
indexes.conf
props.conf
transforms.conf
server.conf
limits.conf
authorize.conf
authentication.conf
web.conf
distsearch.conf
deploymentclient.conf
```

---

# Splunk Enterprise Mental Model

The easiest way to understand Splunk Enterprise is:

```text
                         SPLUNK ENTERPRISE
                                │
          ┌─────────────────────┼──────────────────────┐
          │                     │                      │
          ▼                     ▼                      ▼
       COLLECT                STORE                  SEARCH
          │                     │                      │
     Forwarders             Indexers              Search Heads
          │                     │                      │
          └─────────────────────┼──────────────────────┘
                                │
                                ▼
                         ANALYZE / ACT
                                │
                    ┌───────────┼───────────┐
                    ▼           ▼           ▼
                Dashboards    Alerts      Reports
```

At the enterprise level, additional components provide:

```text
Cluster Manager
       │
       └──► Indexer Cluster

Deployer
       │
       └──► Search Head Cluster

Deployment Server
       │
       └──► Forwarders

License Manager
       │
       └──► License Management

Monitoring Console
       │
       └──► Splunk Infrastructure Monitoring
```

The fundamental Splunk workflow remains:

```text
COLLECT
   ↓
FORWARD
   ↓
PARSE
   ↓
INDEX
   ↓
SEARCH
   ↓
ANALYZE
   ↓
ALERT / REPORT / VISUALIZE
```

This workflow is the foundation for understanding Splunk Enterprise administration, architecture, troubleshooting, and security operations.

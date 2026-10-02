# Ansible

> Foundational technical documentation covering Ansible purpose, components, architecture, usage, configuration, dependencies, and licensing.

---

# 1. Purpose

## 1.1 What is Ansible?

Ansible is an automation platform used to **provision, configure, deploy, orchestrate, and manage systems and applications**.

Ansible is commonly used to automate:

* Linux configuration
* Windows configuration
* Application deployment
* Package installation
* User management
* Service management
* Security configuration
* Network configuration
* Cloud infrastructure
* Container environments
* Application releases
* Infrastructure operations

Instead of manually configuring every server:

```text
Server 01 → Install Package
Server 02 → Install Package
Server 03 → Install Package
Server 04 → Install Package
```

Ansible allows the administrator to define the desired configuration once:

```text
                 Ansible
                    │
          ┌─────────┼─────────┐
          ▼         ▼         ▼
       Server01  Server02  Server03
          │         │         │
          └─────────┼─────────┘
                    ▼
             Same Configuration
```

---

## 1.2 Automation Problem

Without automation, infrastructure configuration can become inconsistent.

Example:

```text
Server 01
 ├── RHEL
 ├── Splunk
 ├── Firewall configured
 └── SSL configured

Server 02
 ├── RHEL
 ├── Splunk
 ├── Firewall missing
 └── SSL configured

Server 03
 ├── RHEL
 ├── Splunk
 ├── Firewall configured
 └── SSL missing
```

This creates **configuration drift**.

Ansible can define the intended configuration:

```text
Desired State
     │
     ▼
  Ansible
     │
 ┌───┼───┐
 ▼   ▼   ▼
S01 S02 S03
 │   │   │
 └───┼───┘
     ▼
Consistent State
```

---

## 1.3 Configuration Management

Ansible can ensure systems remain configured according to a defined state.

For example:

```yaml
- name: Install Splunk
  ansible.builtin.package:
    name: splunk
    state: present
```

The important concept is:

```text
Desired State
     ↓
Ansible
     ↓
Managed System
```

Ansible determines what needs to change rather than requiring the administrator to manually perform every command.

---

## 1.4 Provisioning

Ansible can automate the configuration of newly created systems.

Example:

```text
New EC2 Instance
       │
       ▼
Ansible
       │
       ├── Update OS
       ├── Install Packages
       ├── Configure Firewall
       ├── Create Users
       ├── Configure SSL
       └── Install Application
       │
       ▼
Ready Server
```

---

## 1.5 Orchestration

Ansible can coordinate multiple systems and perform operations in a specific order.

Example:

```text
1. Configure Database
        │
        ▼
2. Configure Application Server
        │
        ▼
3. Configure Web Server
        │
        ▼
4. Start Services
        │
        ▼
5. Validate Deployment
```

---

## 1.6 Common Use Cases

| Use Case                 | Description                               |
| ------------------------ | ----------------------------------------- |
| Configuration Management | Maintain consistent system configurations |
| Provisioning             | Configure newly created systems           |
| Application Deployment   | Deploy applications and updates           |
| Patch Management         | Install OS/software updates               |
| Security Hardening       | Apply security configurations             |
| User Management          | Create and manage users                   |
| Service Management       | Start, stop, enable services              |
| Cloud Automation         | Configure cloud infrastructure            |
| Network Automation       | Configure network devices                 |
| Orchestration            | Coordinate multi-system workflows         |
| Compliance               | Enforce configuration requirements        |
| Disaster Recovery        | Rebuild infrastructure from code          |

---

# 2. Components

The major Ansible concepts are:

```text
Ansible
│
├── Control Node
├── Managed Nodes
├── Inventory
├── Playbooks
├── Plays
├── Tasks
├── Modules
├── Collections
├── Roles
├── Variables
├── Facts
├── Handlers
├── Templates
├── Files
├── Vault
└── Plugins
```

---

# 2.1 Control Node

The Control Node is the system from which Ansible commands and playbooks are executed.

Example:

```text
             Control Node
                  │
          ┌───────┼───────┐
          ▼       ▼       ▼
       Server01 Server02 Server03
```

The Control Node typically contains:

```text
Ansible
Python
Inventory
Playbooks
Roles
Collections
Credentials
Configuration
```

A Control Node can be:

* Linux server
* Administrator workstation
* CI/CD runner
* Cloud VM
* Automation server

---

# 2.2 Managed Nodes

Managed Nodes are systems Ansible controls.

Examples:

```text
Linux Servers
Windows Servers
Network Devices
Cloud Instances
Applications
```

Example:

```text
Control Node
     │
     ├──► RHEL Server
     ├──► Ubuntu Server
     ├──► Windows Server
     ├──► Network Device
     └──► Cloud Instance
```

---

# 2.3 Inventory

Inventory defines the systems Ansible manages.

Example:

```ini
[web]
web01
web02

[database]
db01
db02

[splunk]
idx01
idx02
idx03
sh01
sh02
```

Inventory can be:

* Static
* Dynamic
* YAML
* INI
* Plugin-based

---

# 2.4 Groups

Inventory groups allow systems to be logically organized.

Example:

```ini
[indexers]
idx01
idx02
idx03

[search_heads]
sh01
sh02
sh03

[forwarders]
uf01
uf02
```

A playbook can target a specific group:

```yaml
- name: Configure Splunk Indexers
  hosts: indexers
  tasks:
    ...
```

---

# 2.5 Playbook

A Playbook is an automation document written in YAML.

Example:

```yaml
---
- name: Configure Web Servers
  hosts: web
  become: true

  tasks:
    - name: Install nginx
      ansible.builtin.package:
        name: nginx
        state: present

    - name: Start nginx
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: true
```

A playbook describes:

```text
What
Where
How
```

---

# 2.6 Play

A play maps a group of hosts to a collection of tasks.

Example:

```yaml
- name: Configure Splunk Indexers
  hosts: indexers
  become: true

  tasks:
    ...
```

Here:

```text
Play
│
├── Name
├── Target Hosts
├── Variables
├── Become
└── Tasks
```

---

# 2.7 Task

A task is a single unit of work.

Example:

```yaml
- name: Install curl
  ansible.builtin.package:
    name: curl
    state: present
```

Another:

```yaml
- name: Start Splunk
  ansible.builtin.service:
    name: Splunkd
    state: started
```

---

# 2.8 Module

Modules perform the actual operations.

Examples:

```text
ansible.builtin.package
ansible.builtin.service
ansible.builtin.user
ansible.builtin.copy
ansible.builtin.template
ansible.builtin.file
ansible.builtin.command
ansible.builtin.shell
ansible.builtin.uri
```

Example:

```yaml
- name: Create splunk user
  ansible.builtin.user:
    name: splunk
    state: present
```

The module handles the underlying operation.

---

# 2.9 Roles

Roles provide a reusable structure for organizing automation.

Example:

```text
roles/
└── splunk/
    ├── defaults/
    ├── handlers/
    ├── tasks/
    ├── templates/
    ├── files/
    ├── vars/
    └── meta/
```

A role might perform:

```text
Splunk Role
│
├── Install Splunk
├── Create User
├── Configure Directories
├── Configure Systemd
├── Configure Firewall
└── Start Splunk
```

---

# 2.10 Collections

Collections package Ansible content.

A collection can contain:

* Modules
* Roles
* Plugins
* Playbooks

Example:

```text
namespace.collection
```

Collections make automation easier to distribute and reuse.

---

# 2.11 Variables

Variables allow playbooks to use configurable values.

Example:

```yaml
splunk_version: "10.4.0"
splunk_home: "/opt/splunk"
splunk_user: "splunk"
```

The playbook can reference them:

```yaml
- name: Install Splunk
  ansible.builtin.package:
    name: "splunk-{{ splunk_version }}"
    state: present
```

---

# 2.12 Facts

Facts are information Ansible gathers about managed systems.

Examples:

```text
Operating System
IP Address
Hostname
CPU
Memory
Interfaces
Disks
Architecture
```

Example:

```yaml
- name: Display operating system
  ansible.builtin.debug:
    var: ansible_facts['distribution']
```

---

# 2.13 Handlers

Handlers are tasks triggered when another task reports a change.

Example:

```yaml
tasks:

  - name: Update configuration
    ansible.builtin.template:
      src: app.conf.j2
      dest: /etc/app/app.conf
    notify: Restart application

handlers:

  - name: Restart application
    ansible.builtin.service:
      name: app
      state: restarted
```

The service is restarted only when the configuration changes.

---

# 2.14 Templates

Templates use Jinja2 syntax to generate configuration files dynamically.

Example:

```jinja2
server {
    listen {{ web_port }};
    server_name {{ server_name }};
}
```

Variables can be supplied by Ansible:

```yaml
web_port: 443
server_name: example.com
```

---

# 2.15 Ansible Vault

Ansible Vault protects sensitive information.

Examples:

```text
Passwords
API Keys
Private Keys
Tokens
Credentials
```

Example:

```bash
ansible-vault encrypt secrets.yml
```

Encrypted variables can then be used by playbooks.

---

# 3. Architecture

## 3.1 Basic Architecture

Ansible generally uses an agentless architecture.

```text
                  Control Node
                       │
          ┌────────────┼────────────┐
          │            │            │
          ▼            ▼            ▼
       Server01     Server02     Server03
          │            │            │
       Managed       Managed      Managed
        Node          Node         Node
```

Ansible does not normally require an Ansible agent permanently installed on Linux managed nodes.

---

# 3.2 Linux Architecture

Linux systems commonly use SSH.

```text
             Control Node
                  │
                  │ SSH
                  ▼
             Linux Server
                  │
                  ▼
             Python / OS
```

The Control Node connects to the managed node and executes the required module.

---

# 3.3 Windows Architecture

Windows management commonly uses WinRM or other supported connection mechanisms.

```text
Control Node
     │
     │ WinRM
     ▼
Windows Server
```

---

# 3.4 Network Automation

Ansible can manage network devices.

```text
             Ansible
                │
       ┌────────┼────────┐
       ▼        ▼        ▼
    Router   Switch   Firewall
```

The connection method depends on the network platform.

---

# 3.5 Ansible Execution Model

A simplified execution process:

```text
Playbook
   │
   ▼
Inventory
   │
   ▼
Select Hosts
   │
   ▼
Gather Facts
   │
   ▼
Execute Tasks
   │
   ▼
Modules
   │
   ▼
Managed Node
   │
   ▼
Return Result
```

Example:

```text
Task
 │
 ▼
Module
 │
 ▼
Remote System
 │
 ▼
Changed / OK / Failed
```

---

# 3.6 Ansible and Terraform

Ansible and Terraform are often used together.

Terraform generally focuses on **infrastructure provisioning**.

Ansible generally focuses on **configuration and application management**.

Example:

```text
Terraform
   │
   ▼
Create AWS EC2 Instances
   │
   ▼
Infrastructure Exists
   │
   ▼
Ansible
   │
   ├── Install Packages
   ├── Configure OS
   ├── Configure Firewall
   ├── Install Splunk
   └── Configure Splunk
   │
   ▼
Application Ready
```

A common division is:

```text
Terraform = Provision Infrastructure

Ansible = Configure Infrastructure
```

The two tools can also overlap depending on the implementation.

---

# 3.7 Ansible Splunk Architecture

For a Splunk environment, Ansible can manage:

```text
                    Ansible Control Node
                            │
             ┌──────────────┼──────────────┐
             ▼              ▼              ▼
          Splunk          Splunk         Splunk
         Indexers      Search Heads    Forwarders
             │              │              │
             ▼              ▼              ▼
          IDX01            SH01            UF01
          IDX02            SH02            UF02
          IDX03            SH03            UF03
```

Roles can separate responsibilities:

```text
roles/
├── common/
├── firewall/
├── splunk/
├── indexer_cluster/
├── search_head_cluster/
├── deployment_server/
├── universal_forwarder/
└── ssl/
```

---

# 4. Usage

## 4.1 Check Ansible Version

```bash
ansible --version
```

---

# 4.2 Test Connectivity

```bash
ansible all -m ping
```

Expected result:

```text
server01 | SUCCESS => {
    "ping": "pong"
}
```

---

# 4.3 Run a Command

```bash
ansible all -m command -a "hostname"
```

---

# 4.4 Gather Facts

```bash
ansible all -m setup
```

This retrieves system information from managed nodes.

---

# 4.5 Execute a Playbook

```bash
ansible-playbook site.yml
```

Specify an inventory:

```bash
ansible-playbook -i inventory/hosts site.yml
```

---

# 4.6 Limit Execution

Run against a specific host:

```bash
ansible-playbook -i inventory/hosts site.yml --limit idx01
```

Run against a group:

```bash
ansible-playbook -i inventory/hosts site.yml --limit indexers
```

---

# 4.7 Dry Run

Use check mode:

```bash
ansible-playbook site.yml --check
```

This allows administrators to preview potential changes where supported by the relevant modules.

---

# 4.8 Diff Mode

For configuration changes:

```bash
ansible-playbook site.yml --diff
```

This can show differences between the existing and desired configuration.

---

# 4.9 Tags

Tasks can be assigned tags.

Example:

```yaml
- name: Configure firewall
  ansible.builtin.include_role:
    name: firewall
  tags:
    - firewall
```

Run:

```bash
ansible-playbook site.yml --tags firewall
```

---

# 4.10 Variables

Variables can be supplied through multiple mechanisms.

Example:

```yaml
splunk_version: "10.4.0"
splunk_home: "/opt/splunk"
```

Use:

```yaml
{{ splunk_version }}
```

---

# 4.11 Conditionals

Example:

```yaml
- name: Install package on Red Hat systems
  ansible.builtin.package:
    name: vim
    state: present
  when: ansible_facts['os_family'] == "RedHat"
```

---

# 4.12 Loops

Example:

```yaml
- name: Create Splunk directories
  ansible.builtin.file:
    path: "{{ item }}"
    state: directory
  loop:
    - /opt/splunk
    - /opt/splunk/etc
    - /opt/splunk/var
```

---

# 4.13 Idempotency

Idempotency is a fundamental Ansible concept.

A playbook should be safe to run repeatedly without continuously making unnecessary changes.

Example:

```yaml
- name: Ensure nginx is installed
  ansible.builtin.package:
    name: nginx
    state: present
```

First execution:

```text
CHANGED
```

Second execution:

```text
OK
```

The desired state has already been achieved.

---

# 5. Configuration

## 5.1 ansible.cfg

Ansible's main configuration file is commonly:

```text
ansible.cfg
```

Example:

```ini
[defaults]
inventory = ./inventory
remote_user = ansible
host_key_checking = False
```

Configuration can exist at different levels.

---

# 5.2 Inventory

Example INI inventory:

```ini
[indexers]
idx01
idx02
idx03

[search_heads]
sh01
sh02
sh03

[forwarders]
uf01
uf02
```

---

# 5.3 YAML Inventory

Example:

```yaml
all:
  children:

    indexers:
      hosts:
        idx01:
        idx02:
        idx03:

    search_heads:
      hosts:
        sh01:
        sh02:
        sh03:
```

---

# 5.4 Group Variables

Example:

```text
inventory/
├── hosts.yml
├── group_vars/
│   ├── all.yml
│   ├── indexers.yml
│   └── search_heads.yml
└── host_vars/
```

Example:

```yaml
# group_vars/indexers.yml

splunk_role: indexer
splunk_replication_factor: 3
splunk_search_factor: 2
```

---

# 5.5 Host Variables

Host-specific settings can be stored separately.

Example:

```yaml
# host_vars/idx01.yml

splunk_management_ip: 172.16.10.101
splunk_server_name: idx01
```

---

# 5.6 Role Structure

A typical role:

```text
roles/splunk/
├── defaults/
│   └── main.yml
├── handlers/
│   └── main.yml
├── tasks/
│   └── main.yml
├── templates/
├── files/
├── vars/
│   └── main.yml
└── meta/
    └── main.yml
```

---

# 5.7 Defaults

Default variables should generally contain values that users can easily override.

Example:

```yaml
splunk_user: splunk
splunk_group: splunk
splunk_home: /opt/splunk
splunk_port: 8089
```

---

# 5.8 Templates

Example:

```text
templates/
├── server.conf.j2
├── inputs.conf.j2
└── outputs.conf.j2
```

Example:

```jinja2
[general]
serverName = {{ splunk_server_name }}
```

---

# 5.9 Secrets

Sensitive values should not be stored in plaintext.

Avoid:

```yaml
password: MyPassword123
```

Prefer:

```text
Ansible Vault
```

Example:

```bash
ansible-vault create secrets.yml
```

---

# 5.10 SSH Configuration

Linux management commonly requires:

```text
Control Node
     │
     │ SSH
     ▼
Managed Node
```

The Control Node needs:

* Network access
* SSH access
* Authentication
* Appropriate privileges

---

# 5.11 Privilege Escalation

Ansible can use privilege escalation.

Example:

```yaml
- hosts: all
  become: true

  tasks:
    - name: Install package
      ansible.builtin.package:
        name: vim
        state: present
```

Commonly:

```text
User
 │
 ▼
sudo
 │
 ▼
root
```

---

# 6. Dependencies

## 6.1 Control Node

The Control Node requires:

* Supported operating system
* Python
* Ansible
* Network connectivity
* Credentials

---

# 6.2 Managed Linux Nodes

Linux managed nodes commonly require:

* SSH
* Python
* Appropriate user permissions
* Network connectivity

---

# 6.3 Managed Windows Nodes

Windows management can require:

* WinRM
* Appropriate Windows configuration
* Authentication
* Network connectivity

---

# 6.4 Network

Ansible requires connectivity between the Control Node and managed systems.

Example:

```text
Control Node
     │
     │ SSH / WinRM / Network API
     ▼
Managed Node
```

Firewall rules must permit the required connection method.

---

# 6.5 Python

Many Ansible modules require Python on managed Linux systems.

Verify:

```bash
python3 --version
```

The required Python version depends on the Ansible release and module being used.

---

# 6.6 Credentials

Ansible requires authentication to managed systems.

Common approaches include:

```text
SSH Keys
Passwords
Vault
Cloud Credentials
API Tokens
Certificates
```

Credentials should be protected appropriately.

---

# 6.7 Cloud Dependencies

Ansible can manage cloud platforms through collections and APIs.

Example:

```text
Ansible
   │
   ▼
Cloud API
   │
   ▼
AWS / Azure / GCP
```

Cloud automation may require:

* API credentials
* IAM permissions
* Network access
* Cloud SDK dependencies
* Appropriate Ansible collections

---

# 6.8 Collections

Some automation requires additional collections.

Example:

```bash
ansible-galaxy collection install amazon.aws
```

Collections may provide:

* Cloud modules
* Network modules
* Vendor-specific modules
* Specialized plugins

---

# 6.9 Git

Git is not required for Ansible itself, but it is commonly used to manage Ansible code.

Example:

```text
Git Repository
│
├── inventory/
├── playbooks/
├── roles/
├── group_vars/
├── host_vars/
└── ansible.cfg
```

Git provides:

* Version control
* Change history
* Collaboration
* Branching
* Rollback

---

# 6.10 CI/CD

Ansible can be integrated into CI/CD systems.

Example:

```text
Git
 │
 ▼
CI/CD Pipeline
 │
 ▼
Ansible
 │
 ▼
Infrastructure
```

This allows automation to be tested and deployed in a controlled workflow.

---

# 7. License / Cost

## 7.1 Ansible Licensing

Ansible is available as open-source software.

The upstream Ansible project is distributed under an open-source license.

Organizations can use the open-source Ansible tooling without purchasing a traditional per-node commercial license for the upstream project.

---

# 7.2 Ansible Automation Platform

Red Hat provides **Ansible Automation Platform**, a commercial enterprise automation offering.

It provides additional enterprise capabilities around the Ansible ecosystem.

Conceptually:

```text
Open-Source Ansible
        │
        ▼
Core Automation Engine
        │
        ▼
Ansible Automation Platform
        │
        ├── Enterprise Automation
        ├── Automation Controller
        ├── Automation Hub
        ├── Governance
        └── Enterprise Support
```

The exact capabilities and licensing depend on the current Red Hat offering.

---

# 7.3 Open-Source Ansible vs Enterprise Platform

| Area                   | Ansible Community               | Ansible Automation Platform |
| ---------------------- | ------------------------------- | --------------------------- |
| Automation             | Yes                             | Yes                         |
| Playbooks              | Yes                             | Yes                         |
| Modules                | Yes                             | Yes                         |
| Roles                  | Yes                             | Yes                         |
| Collections            | Yes                             | Yes                         |
| CLI                    | Yes                             | Yes                         |
| Enterprise UI          | Limited / external tooling      | Yes                         |
| Centralized Automation | Requires additional tooling     | Yes                         |
| Enterprise Governance  | Limited                         | Yes                         |
| Vendor Support         | Community                       | Commercial support          |
| Commercial License     | No traditional per-node license | Subscription                |

The exact feature set should be verified against the current Red Hat product documentation.

---

# 7.4 Infrastructure Costs

Even when using open-source Ansible, automation can have infrastructure costs.

Example:

```text
Ansible Cost
│
├── Control Node
├── Managed Infrastructure
├── Cloud Resources
├── Storage
├── Network
├── Git / CI Infrastructure
└── Administration
```

---

# 7.5 Operational Cost

Automation can reduce repetitive manual work.

Example:

```text
Manual:

Administrator
   │
   ├── Server 01
   ├── Server 02
   ├── Server 03
   ├── Server 04
   └── Server 05


Ansible:

Administrator
      │
      ▼
   Playbook
      │
 ┌────┼────┬────┬────┐
 ▼    ▼    ▼    ▼    ▼
 S01  S02  S03  S04  S05
```

However, automation itself requires:

* Development
* Testing
* Maintenance
* Documentation
* Code review
* Troubleshooting

---

# Quick Reference

## Core Components

| Component    | Purpose                             |
| ------------ | ----------------------------------- |
| Control Node | Executes Ansible                    |
| Managed Node | System being managed                |
| Inventory    | Defines managed systems             |
| Playbook     | Defines automation workflow         |
| Play         | Maps tasks to hosts                 |
| Task         | Individual unit of work             |
| Module       | Performs an operation               |
| Role         | Reusable automation structure       |
| Collection   | Package of Ansible content          |
| Variable     | Configurable value                  |
| Fact         | Information about managed system    |
| Handler      | Executes when notified              |
| Template     | Dynamically generates configuration |
| Vault        | Protects sensitive data             |

---

## Common Commands

```bash
# Check version
ansible --version

# Test connectivity
ansible all -m ping

# Run a command
ansible all -m command -a "hostname"

# Gather facts
ansible all -m setup

# Run playbook
ansible-playbook site.yml

# Specify inventory
ansible-playbook -i inventory/hosts site.yml

# Limit hosts
ansible-playbook site.yml --limit idx01

# Check mode
ansible-playbook site.yml --check

# Show configuration differences
ansible-playbook site.yml --diff

# Encrypt secrets
ansible-vault encrypt secrets.yml
```

---

# Ansible Mental Model

The easiest way to understand Ansible is:

```text
                    ANSIBLE
                       │
                       ▼
                   INVENTORY
                       │
                       ▼
                   PLAYBOOK
                       │
                       ▼
                     PLAY
                       │
                       ▼
                     TASK
                       │
                       ▼
                    MODULE
                       │
                       ▼
                 MANAGED NODE
                       │
                       ▼
                 DESIRED STATE
```

The fundamental automation workflow is:

```text
DEFINE
   ↓
TARGET
   ↓
CONFIGURE
   ↓
EXECUTE
   ↓
VALIDATE
   ↓
MAINTAIN
```

For infrastructure engineering, the key concept is:

> **Define the desired state as code, apply it consistently across systems, and make the automation repeatable and idempotent.**

For a Splunk environment, this becomes:

```text
                    ANSIBLE
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
       INDEXERS    SEARCH HEADS   FORWARDERS
          │            │            │
          ▼            ▼            ▼
      Splunk Config  Splunk Config  Splunk Config
          │            │            │
          └────────────┼────────────┘
                       ▼
                Consistent
              Splunk Environment
```

This is particularly useful when managing a distributed Splunk deployment where manually configuring every Indexer, Search Head, Forwarder, Deployment Server, or supporting service would be time-consuming and prone to configuration drift.

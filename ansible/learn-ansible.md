# Ansible YAML Basics
- Basic structure of an Ansible playbook.

name - Human-readable description.
hosts - Defines which servers the playbook targets.
become - Privilege escalation (similar to sudo).
tasks - A list of actions Ansible executes.

Ansible YAML file hierarchy
```
Playbook
 └── Play
      ├── name
      ├── hosts
      ├── become
      └── tasks
            └── task
                 ├── name
                 └── module
```

```
---
- name: Install Splunk Enterprise
  hosts: splunk
  become: yes

  tasks:
    - name: Create a splunk user
      user:
        name: splunk
```

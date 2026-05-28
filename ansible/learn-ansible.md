# Ansible YAML Basics
- Basic structure of an Ansible playbook.

1. name - Human-readable description.
2. hosts - Defines which servers the playbook targets.
3. become - Privilege escalation (similar to sudo).
4. tasks - A list of actions Ansible executes.

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

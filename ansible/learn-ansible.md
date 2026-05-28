# Ansible YAML Basics

Ansible playbooks are written in **YAML** and define a set of instructions for automating tasks on target hosts.

---

## 1. Basic Structure of a Playbook

| Key       | Description |
|-----------|-------------|
| **name**  | Human-readable description of the play or task. |
| **hosts** | Specifies which servers or groups the playbook targets. |
| **become**| Enables privilege escalation (like `sudo`). |
| **tasks** | A list of actions (tasks) Ansible executes. |

---

## 2. Playbook Hierarchy

```text
Playbook
 └── Play
      ├── name     # Description of the play
      ├── hosts    # Target hosts
      ├── become   # Privilege escalation
      └── tasks    # List of tasks
            └── task
                 ├── name   # Task description
                 └── module # Action to perform (e.g., user, package)
```

## 3. Example Playbook

```yaml
---
- name: Install Splunk Enterprise
  hosts: splunk
  become: yes

  tasks:
    - name: Create a splunk user
      user:
        name: splunk
```
- Play: Installs Splunk Enterprise on the splunk hosts.
- Task: Creates a user named splunk using the user module.
- Privilege escalation: become: yes ensures the task runs with elevated privileges.

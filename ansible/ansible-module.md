# Ansible Module

## Group
```
ansible.builtin.group:
  name:       # What group?
  state:      # Should it exist or not?
  system:     # System group or regular group?
```
```
 ansible.builtin.group:
    name: splunk
    state: present/absent
    system: true/false
```

## User
```
ansible.builtin.user:
  name: splunk
  group: splunk
  create_home: true
  home: /home/splunk
  shell: /sbin/nologin
  system: true
  state: present
```
```
ansible.builtin.user:
  name:          # What user?
  group:         # What is the user's primary group?
  system:        # System user or regular user?
  create_home:   # Create the user's home directory?
  home:          # Where should the home directory be?
  shell:         # What login shell should the user have?
  password:      # What password/hash should the user have?
  state:         # Should the user exist or not?
```

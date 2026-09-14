# Ansible Module

## Common state values
```
state: present    # Make sure package is installed
state: absent     # Make sure package is removed
state: latest     # Make sure latest version is installed
```

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

## Directory
```
ansible.builtin.file:
  path:       # What directory?
  state:      # Should it exist or not?
  owner:      # Who should own the directory?
  group:      # What group should own the directory?
  mode:       # What permissions should the directory have?
```
```
ansible.builtin.file:
  path: /opt/splunk
  state: directory
  owner: splunk
  group: splunk
  mode: "0755"
```

## YUM, DNF, APT

```
ansible.builtin.yum:
  name:     # What package?
  state:    # Should it be installed or removed?
```
```
ansible.builtin.yum:
  name:
    - wget
  state: present
```

## Copy

```
ansible.builtin.copy:
  src:             # Local source file
  dest:            # Remote destination
  owner:           # File owner
  group:           # File group
  mode:            # File permissions
  backup:          # Create backup before overwriting
  force:           # Overwrite existing file
  remote_src:      # Source file is already on remote server
```
```
ansible.builtin.copy:
  src: files/example.conf
  dest: /etc/example.conf
  owner: root
  group: root
  mode: "0644"
```

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
```
state: present    # Make sure package is installed
state: absent     # Make sure package is removed
state: latest     # Make sure latest version is installed
```

## Copy
```
ansible.builtin.copy:
  src:        # Source file on the Ansible controller
  dest:       # Destination path on the Linux server
  owner:      # Who should own the file?
  group:      # What group should own the file?
  mode:       # What permissions should the file have?
```
```
ansible.builtin.copy:
  src: files/example.conf
  dest: /etc/example.conf
  owner: root
  group: root
  mode: "0644"
```
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
## Download

```
ansible.builtin.get_url:
  url:        # URL of the file to download
  dest:       # Where to save the file
  owner:      # Who should own the file?
  group:      # What group should own the file?
  mode:       # What permissions should the file have?
```
```
ansible.builtin.get_url:
  url: https://example.com/application.tar.gz
  dest: /opt/application.tar.gz
  owner: root
  group: root
  mode: "0644"
```
```
ansible.builtin.get_url:
  url:             # Download URL
  dest:            # Destination file
  owner:           # File owner
  group:           # File group
  mode:            # File permissions
  checksum:        # Verify downloaded file
  force:           # Force download if needed
  timeout:         # Download timeout
```

## Unarchive
```
ansible.builtin.unarchive:
  src:         # Archive file to extract
  dest:        # Directory where files should be extracted
  remote_src:  # Is the archive already on the Linux server?
  owner:       # Who should own extracted files?
  group:       # What group should own extracted files?
  mode:        # Permissions for extracted files
```
```
ansible.builtin.unarchive:
  src: application.tar.gz
  dest: /opt/application
```
```
ansible.builtin.unarchive:
  src:          # Archive source
  dest:         # Extraction directory
  remote_src:   # Source is on remote server?
  owner:        # Owner of extracted files
  group:        # Group of extracted files
  mode:         # Permissions
  creates:      # Skip extraction if this file already exists
```

## Command
```
ansible.builtin.command:
  cmd:       # What command should be executed?
  chdir:     # Which directory should the command run from?
  creates:   # Skip command if this file already exists
  removes:   # Skip command if this file does not exist
```
```
ansible.builtin.command:
  cmd: ./soar-install
  chdir: /opt/phantom
```

## Shell
```
ansible.builtin.shell:
  cmd:       # Shell command
  chdir:     # Working directory
  creates:   # Skip if file exists
  removes:   # Skip if file doesn't exist
```
```
- name: OS - Check disk space
  ansible.builtin.shell:
    cmd: df -h /opt | tail -n 1
```

## Stat
```
ansible.builtin.stat:
  path:       # File or directory to check
```
```
- name: OS - Check Splunk SOAR installation directory
  ansible.builtin.stat:
    path: /opt/phantom
  register: splunk_soar_dir
```
## Assert
```
ansible.builtin.assert:
  that:       # Conditions that must be true
  fail_msg:   # Message if validation fails
  success_msg: # Message if validation succeeds
```
```
- name: OS - Verify Splunk SOAR directory exists
  ansible.builtin.assert:
    that:
      - splunk_soar_dir.stat.exists
    fail_msg: "Splunk SOAR directory does not exist"
```

## Systemd Service
```
ansible.builtin.systemd_service:
  name:        # What service?
  state:       # What should the service be doing?
  enabled:     # Should it start automatically at boot?
```
```
ansible.builtin.systemd_service:
  name: firewalld
  state: started
  enabled: true
```
```
state: started     # Start the service
state: stopped     # Stop the service
state: restarted   # Restart the service
state: reloaded    # Reload configuration
```
```
enabled: true      # Start automatically at boot
enabled: false     # Do not start automatically at boot
```

## Firewalld
```
ansible.posix.firewalld:
  service:      # Which predefined service?
  port:         # Which port/protocol?
  zone:         # Which firewall zone?
  state:        # add / remove
  permanent:    # Persist after reboot?
  immediate:    # Apply to the running firewall now?
```
```
- name: Firewall - Allow SSH
  ansible.posix.firewalld:
    service: ssh
    state: enabled
    permanent: true
    immediate: true
```

## Template
```
ansible.builtin.template:
  src:        # Jinja2 template on the Ansible controller
  dest:       # Destination file on the Linux server
  owner:      # Who should own the file?
  group:      # What group should own the file?
  mode:       # What permissions should the file have?
```
```
- name: OS - Configure application
  ansible.builtin.template:
    src: application.conf.j2
    dest: /etc/application.conf
    owner: root
    group: root
    mode: "0644"
```
```
ansible.builtin.template:
  src:              # Source .j2 template
  dest:             # Destination
  owner:            # File owner
  group:            # File group
  mode:             # File permissions
  backup:           # Backup existing file before changing
  validate:         # Validate configuration before replacing
```
Blockinfile
```
ansible.builtin.blockinfile:
  path:         # Which file should be modified?
  block:        # What block of text should be added?
  marker:       # What markers identify the block?
  state:        # Should the block exist or be removed?
  owner:        # Who should own the file?
  group:        # What group should own the file?
  mode:         # What permissions should the file have?
```
```
- name: OS - Configure custom settings
  ansible.builtin.blockinfile:
    path: /etc/example.conf
    block: |
      setting1=value1
      setting2=value2
      setting3=value3
    marker: "# {mark} ANSIBLE MANAGED BLOCK"
    state: present
    owner: root
    group: root
    mode: "0644"
```
```
# BEGIN ANSIBLE MANAGED BLOCK
setting1=value1
setting2=value2
setting3=value3
# END ANSIBLE MANAGED BLOCK
```

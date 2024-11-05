# Understanding ansible.cfg

The `ansible.cfg` is the configuration file for Ansible that controls its behavior. Here's a brief overview:

## Key Points
- Default location: `/etc/ansible/ansible.cfg`
- Can be placed in: 
 - `/etc/ansible/ansible.cfg` (global)
 - `~/.ansible.cfg` (user-specific)
 - `./ansible.cfg` (project directory - highest priority)

## Common Settings
```ini
[defaults]
inventory = ./inventory
remote_user = ansible
host_key_checking = False
forks = 5
timeout = 30
log_path = /var/log/ansible.log

[privilege_escalation]
become = True
become_method = sudo
become_user = root
become_ask_pass = False

[ssh_connection]
pipelining = True
control_path = /tmp/ansible-ssh-%%h-%%p-%%r

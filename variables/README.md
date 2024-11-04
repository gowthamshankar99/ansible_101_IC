# Ansible Variables and Order of Precedence

## Introduction
Understanding variable precedence in Ansible is crucial for effective playbook management. This guide explains how Ansible processes variables and their order of precedence, from lowest to highest priority.

## Variable Precedence Order

1. **Role Defaults (lowest priority)**
   - Location: `roles/x/defaults/main.yml`
   - Purpose: Default values that can be easily overridden
   - Example:
   ```yaml
   # roles/webserver/defaults/main.yml
   http_port: 80
   max_clients: 100
   ```

2. **Inventory Variables**
   - **Group Variables from Inventory File**
   ```ini
   [webservers:vars]
   http_port=8080
   ```
   - **Host Variables from Inventory File**
   ```ini
   webserver1 http_port=8081
   ```

3. **Group Variables (group_vars)**
   - Location: `group_vars/group_name.yml`
   - Purpose: Variables specific to groups of hosts
   ```yaml
   # group_vars/webservers.yml
   http_port: 8080
   ntp_server: ntp.example.com
   ```

4. **Host Variables (host_vars)**
   - Location: `host_vars/hostname.yml`
   - Purpose: Host-specific variables
   ```yaml
   # host_vars/webserver1.yml
   http_port: 8082
   backup_dir: /var/backups
   ```

5. **Play Variables (from playbook)**
   ```yaml
   - hosts: webservers
     vars:
       http_port: 8083
       max_clients: 200
   ```

6. **Play Variables Included from Files**
   ```yaml
   - hosts: webservers
     vars_files:
       - vars/common.yml
       - vars/production.yml
   ```

7. **Play Variables from Blocks**
   ```yaml
   - hosts: webservers
     tasks:
       - block:
           - name: Install nginx
             apt:
               name: nginx
         vars:
           http_port: 8084
   ```

8. **Task Variables**
   ```yaml
   - name: Install package
     apt:
       name: nginx
     vars:
       custom_var: value
   ```

9. **Register Variables**
   ```yaml
   - name: Check service status
     command: systemctl status nginx
     register: service_status
   ```

10. **Set Facts**
    ```yaml
    - name: Set fact
      set_fact:
        http_port: 8085
    ```

11. **Extra Variables (highest priority)**
    - Command line variables using `-e` or `--extra-vars`
    ```bash
    ansible-playbook playbook.yml -e "http_port=8086"
    ```

## Special Variable Types

### Magic Variables
- `hostvars`: Contains variables for all hosts
- `inventory_hostname`: Current host being processed
- `groups`: List of all groups in inventory
- `group_names`: List of groups the current host belongs to

### Facts
- Automatically gathered system information
- Can be accessed using `ansible_facts` prefix
- Example: `ansible_facts['os_family']`

## Best Practices

1. **Variable Naming**
   - Use meaningful, descriptive names
   - Follow consistent naming conventions
   - Use underscores instead of hyphens
   - Prefix role variables with role name

2. **Organization**
   - Group related variables together
   - Use separate files for different environments
   - Keep sensitive variables in encrypted files using Ansible Vault

3. **Documentation**
   - Document all variables, especially in roles
   - Include default values and valid options
   - Explain the purpose of each variable

4. **Security**
   - Use Ansible Vault for sensitive data
   - Never commit unencrypted sensitive variables
   - Regularly rotate sensitive values

5. **Maintainability**
   - Use defaults in roles for better reusability
   - Keep environment-specific variables separate
   - Use variable files instead of lengthy inline variables

## Example Variable File Structure
```
ansible/
├── group_vars/
│   ├── all.yml
│   ├── webservers.yml
│   └── dbservers.yml
├── host_vars/
│   ├── web1.yml
│   └── db1.yml
├── vars/
│   ├── common.yml
│   ├── production.yml
│   └── staging.yml
└── roles/
    └── webserver/
        ├── defaults/
        │   └── main.yml
        └── vars/
            └── main.yml
```

## Debugging Variables

To check variable values during playbook execution:
```yaml
- name: Debug variable
  debug:
    var: http_port

- name: Debug verbose
  debug:
    msg: "HTTP port is {{ http_port }} on {{ inventory_hostname }}"
```

You can also use the `ansible-inventory` command to check host variables:
```bash
ansible-inventory --list
ansible-inventory --host hostname
```

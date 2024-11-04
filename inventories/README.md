# Ansible Inventory Structure Guide

This guide demonstrates various ways to structure your Ansible inventories, from basic to complex configurations.

## 1. Basic Static Inventory

### Simple INI Format
```ini
# inventory.ini
web1.example.com
web2.example.com
db1.example.com

[webservers]
web1.example.com
web2.example.com

[dbservers]
db1.example.com
```

### YAML Format Equivalent
```yaml
# inventory.yml
all:
  hosts:
    web1.example.com:
    web2.example.com:
    db1.example.com:
  children:
    webservers:
      hosts:
        web1.example.com:
        web2.example.com:
    dbservers:
      hosts:
        db1.example.com:
```

## 2. Advanced Host Variables

### INI Format with Variables
```ini
# inventory.ini
[webservers]
web1.example.com ansible_host=10.0.0.1 ansible_port=2222 ansible_user=admin
web2.example.com ansible_host=10.0.0.2 http_port=8080 max_memory=512

[webservers:vars]
ansible_ssh_user=ubuntu
http_port=80
max_memory=256

[dbservers]
db[1:3].example.com ansible_user=postgres
db-[a:c].example.com
```

### YAML Format with Variables
```yaml
# inventory.yml
all:
  children:
    webservers:
      hosts:
        web1.example.com:
          ansible_host: 10.0.0.1
          ansible_port: 2222
          ansible_user: admin
        web2.example.com:
          ansible_host: 10.0.0.2
          http_port: 8080
          max_memory: 512
      vars:
        ansible_ssh_user: ubuntu
        http_port: 80
        max_memory: 256
    dbservers:
      hosts:
        db1.example.com:
        db2.example.com:
        db3.example.com:
        db-a.example.com:
        db-b.example.com:
        db-c.example.com:
```

## 3. Nested Groups Structure

### INI Format with Nested Groups
```ini
# inventory.ini
[webservers]
web1.example.com
web2.example.com

[dbservers]
db1.example.com
db2.example.com

[east_datacenter]
web1.example.com
db1.example.com

[west_datacenter]
web2.example.com
db2.example.com

[datacenters:children]
east_datacenter
west_datacenter

[production:children]
webservers
dbservers

# Group variables
[production:vars]
env=production
monitoring=enabled

[east_datacenter:vars]
region=us-east-1
backup_server=backup.east.example.com

[west_datacenter:vars]
region=us-west-1
backup_server=backup.west.example.com
```

### YAML Format with Nested Groups
```yaml
# inventory.yml
all:
  children:
    production:
      children:
        webservers:
          hosts:
            web1.example.com:
            web2.example.com:
        dbservers:
          hosts:
            db1.example.com:
            db2.example.com:
      vars:
        env: production
        monitoring: enabled

    datacenters:
      children:
        east_datacenter:
          hosts:
            web1.example.com:
            db1.example.com:
          vars:
            region: us-east-1
            backup_server: backup.east.example.com
        west_datacenter:
          hosts:
            web2.example.com:
            db2.example.com:
          vars:
            region: us-west-1
            backup_server: backup.west.example.com
```

## 4. Environment-Based Structure

```yaml
# environments/production/inventory.yml
all:
  children:
    prod:
      children:
        prod_webservers:
          hosts:
            prod-web[1:3].example.com:
          vars:
            nginx_worker_processes: 4
            php_memory_limit: 512M
        
        prod_dbservers:
          hosts:
            prod-db-master.example.com:
              mysql_role: master
            prod-db-slave[1:2].example.com:
              mysql_role: slave
          vars:
            mysql_max_connections: 1000
            backup_enabled: true
      
      vars:
        env: production
        monitoring_interval: 60
        alert_email: prod-alerts@example.com

# environments/staging/inventory.yml
all:
  children:
    staging:
      children:
        staging_webservers:
          hosts:
            staging-web1.example.com:
          vars:
            nginx_worker_processes: 2
            php_memory_limit: 256M
        
        staging_dbservers:
          hosts:
            staging-db1.example.com:
          vars:
            mysql_max_connections: 500
            backup_enabled: false
      
      vars:
        env: staging
        monitoring_interval: 300
        alert_email: dev-alerts@example.com
```

## 5. Dynamic Inventory Structure

```python
#!/usr/bin/env python3
# dynamic_inventory.py

import json

def get_inventory():
    return {
        "webservers": {
            "hosts": ["web1.example.com", "web2.example.com"],
            "vars": {
                "http_port": 80,
                "proxy_timeout": 5
            }
        },
        "dbservers": {
            "hosts": ["db1.example.com"],
            "vars": {
                "mysql_port": 3306,
                "mysql_max_connections": 100
            }
        },
        "_meta": {
            "hostvars": {
                "web1.example.com": {
                    "ansible_host": "10.0.0.1",
                    "ansible_user": "ubuntu"
                },
                "web2.example.com": {
                    "ansible_host": "10.0.0.2",
                    "ansible_user": "ubuntu"
                },
                "db1.example.com": {
                    "ansible_host": "10.0.0.3",
                    "ansible_user": "postgres"
                }
            }
        }
    }

if __name__ == "__main__":
    inventory = get_inventory()
    print(json.dumps(inventory, indent=2))
```

## 6. Best Practices for Inventory Organization

### Directory Structure
```
inventory/
├── production/
│   ├── hosts.yml                 # Production inventory file
│   ├── group_vars/
│   │   ├── all.yml              # Variables for all production hosts
│   │   ├── webservers.yml       # Variables for production webservers
│   │   └── dbservers.yml        # Variables for production database servers
│   └── host_vars/
│       ├── web1.example.com.yml # Specific variables for web1
│       └── db1.example.com.yml  # Specific variables for db1
├── staging/
│   ├── hosts.yml                # Staging inventory file
│   ├── group_vars/
│   │   ├── all.yml             # Variables for all staging hosts
│   │   ├── webservers.yml      # Variables for staging webservers
│   │   └── dbservers.yml       # Variables for staging database servers
│   └── host_vars/
│       └── stage-web1.yml      # Specific variables for stage-web1
└── testing/
    └── hosts.yml               # Testing inventory file
```

## 7. Pattern Examples

### Host Patterns
```ini
# Ranges for numbered hosts
web[1:5].example.com    # Matches web1.example.com through web5.example.com
db-[a:c].example.com    # Matches db-a.example.com through db-c.example.com

# Wildcards
*.example.com          # All hosts ending in .example.com
web*.example.com       # All hosts starting with web and ending in .example.com

# Regular expressions (use '~' prefix)
~web\d+\.example\.com  # Matches any web server with numbers
```

### Group Patterns
```ini
webservers:dbservers     # All hosts in webservers and dbservers
webservers:!staging      # Hosts in webservers but not in staging
webservers:&production   # Hosts in both webservers and production
```

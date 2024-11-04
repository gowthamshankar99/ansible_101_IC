# Ansible Vault Guide

Ansible Vault is a feature that allows you to encrypt sensitive data such as passwords, private keys, and API tokens. This guide demonstrates basic usage with practical examples.

## 1. Creating an Encrypted File

First, create an encrypted file to store sensitive variables:

```bash
ansible-vault create secrets.yml
```

This will prompt for a new vault password and open your default editor. Add your sensitive data:

```yaml
---
database_password: supersecret123
api_key: abc123xyz789
admin_user: admin
```

## 2. Creating a Playbook

Create a playbook (`playbook.yml`) that uses the encrypted variables:

```yaml
---
- name: Database configuration example
  hosts: webservers
  vars_files:
    - secrets.yml
  
  tasks:
    - name: Configure database connection
      template:
        src: db_config.j2
        dest: /etc/app/database.conf
      vars:
        db_user: "{{ admin_user }}"
        db_password: "{{ database_password }}"
```

## 3. Template File

Create a template file (`db_config.j2`):

```
DATABASE_URL=postgresql://{{ db_user }}:{{ db_password }}@localhost:5432/myapp
```

## 4. Inventory File

Create an inventory file (`inventory.ini`):

```ini
[webservers]
web1.example.com
web2.example.com
```

## 5. Common Vault Commands

### Viewing Encrypted Files
```bash
ansible-vault view secrets.yml
```

### Editing Encrypted Files
```bash
ansible-vault edit secrets.yml
```

### Encrypting Existing Files
```bash
ansible-vault encrypt existing_file.yml
```

### Decrypting Files
```bash
ansible-vault decrypt secrets.yml
```

### Changing Vault Password
```bash
ansible-vault rekey secrets.yml
```

## 6. Running Playbooks with Vault

### Using Password Prompt
```bash
ansible-playbook -i inventory.ini playbook.yml --ask-vault-pass
```

### Using Password File
```bash
# Create password file
echo "your_vault_password" > vault_pass.txt

# Run playbook with password file
ansible-playbook -i inventory.ini playbook.yml --vault-password-file vault_pass.txt

# Don't forget to secure or delete the password file when done
rm vault_pass.txt
```

## 7. Best Practices

1. **Never commit unencrypted sensitive data** to version control
2. **Don't store vault passwords** in version control
3. Use **different vault passwords** for different environments (dev, staging, prod)
4. Regularly **rotate vault passwords**
5. Use **vault-password-file** in automated deployments
6. Consider using **multiple vault IDs** for different security levels

## 8. Multiple Vault IDs Example

You can use different passwords for different vaults:

```bash
# Create vault with specific ID
ansible-vault create --vault-id dev@prompt dev-secrets.yml
ansible-vault create --vault-id prod@prompt prod-secrets.yml

# Run playbook with multiple vault IDs
ansible-playbook site.yml --vault-id dev@dev-pass.txt --vault-id prod@prod-pass.txt
```

## 9. Troubleshooting

If you encounter issues:

1. Verify the vault password is correct
2. Check file permissions
3. Ensure the vault file is properly formatted YAML
4. Verify the encrypted file wasn't corrupted
5. Check if the vault password file is readable

## 10. Example Project Structure

```
project/
├── inventory/
│   ├── dev.ini
│   └── prod.ini
├── group_vars/
│   ├── all/
│   │   └── secrets.yml  # encrypted
│   └── webservers/
│       └── vars.yml
├── templates/
│   └── db_config.j2
├── playbook.yml
└── ansible.cfg
```

```
# Run only specific tags
ansible-playbook playbook.yml --tags "setup,config"

# Skip specific tags
ansible-playbook playbook.yml --skip-tags "backup,cleanup"

# List all available tags
ansible-playbook playbook.yml --list-tags

# Run all tasks except 'never' tagged
ansible-playbook playbook.yml

# Run only tagged tasks (including 'always' tagged)
ansible-playbook playbook.yml --tags "deploy"

# Run multiple tags
ansible-playbook playbook.yml --tags "setup,deploy,config"

# Skip multiple tags
ansible-playbook playbook.yml --skip-tags "backup,cleanup,restart"
```

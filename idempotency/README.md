# Ansible Idempotency Example

This example demonstrates several key concepts about idempotency in Ansible:

## Key Concepts

1. The first task uses Ansible's built-in `file` module which is already idempotent

2. The second task shows how to make a shell command idempotent by:
  - Using a conditional check in the shell script
  - Outputting a specific string ("CREATED" or "EXISTS")
  - Using `changed_when` to set the task status based on the output

3. The third task shows a more complex example where we:
  - Compare old and new content
  - Only make changes if needed
  - Using `changed_when` to accurately reflect if changes were made

## Running the Playbook

You can run this playbook multiple times and observe:

- **First run:** You'll see changes for creating the directory and file
- **Subsequent runs:** No changes will be reported unless the content actually changes

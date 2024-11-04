# Ansible Loops Guide

## Basic Loop Examples

The simplest way to loop in Ansible is using the `loop` keyword with debug:

```yaml
- name: Basic loop
  debug:
    msg: "Current item: {{ item }}"
  loop:
    - apple
    - banana
    - orange
```

## Looping with Lists of Dictionaries

When you have more complex data structures:

```yaml
- name: Loop through users
  debug:
    msg: "User {{ item.name }} is in role {{ item.role }}"
  loop:
    - { name: 'john', role: 'admin' }
    - { name: 'jane', role: 'user' }
```

## Using Loop Control

You can control loop behavior and rename variables:

```yaml
- name: Loop with index
  debug:
    msg: "Item {{ my_index }} is {{ my_item }}"
  loop:
    - "first"
    - "second"
  loop_control:
    loop_var: my_item
    index_var: my_index
```

## Conditional Loops

Use `when` to filter loop items:

```yaml
- name: Conditional loop
  debug:
    msg: "Found admin: {{ item.name }}"
  loop: "{{ users }}"
  when: item.role == 'admin'
```

## Registering Loop Results

You can capture loop results:

```yaml
- name: Loop with register
  debug:
    msg: "Processing {{ item }}"
  loop:
    - item1
    - item2
  register: results

- name: Show results
  debug:
    var: results.results
```

## Dictionary Loops

Transform dictionaries for looping:

```yaml
- name: Dictionary loop
  debug:
    msg: "Key: {{ item.key }}, Value: {{ item.value }}"
  loop: "{{ my_dict | dict2items }}"
```

## Best Practices

1. Always use meaningful variable names with `loop_control`
2. Use `debug` module for testing loops before implementing actual tasks
3. Keep loop structures simple and readable
4. Use proper YAML formatting for complex data structures
5. Register results when you need to process loop output

## Common Use Cases

- Installing multiple packages
- Creating multiple users
- Processing configuration files
- Managing services
- Iterating through inventory hosts

## Tips for Testing

1. Start with `debug` module to verify loop behavior
2. Use small datasets while testing
3. Verify registered variables with debug
4. Use `-v` flag when running playbooks to see detailed output
5. Check loop conditions with small subsets first

## Variables and Loops

You can store loop data in variables:

```yaml
vars:
  packages:
    - nginx
    - postgresql
    - redis

tasks:
  - name: Debug packages
    debug:
      msg: "Would install {{ item }}"
    loop: "{{ packages }}"
```

## Error Handling in Loops

```yaml
- name: Loop with error handling
  debug:
    msg: "Processing {{ item }}"
  loop: "{{ items }}"
  ignore_errors: yes
```

Remember: Using `debug` module is perfect for testing loop logic before implementing actual tasks that modify systems.

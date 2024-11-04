# Ansible Conditions Guide

## Basic Conditions

The simplest form uses `when`:

```yaml
- name: Basic condition
  debug:
    msg: "This will run"
  when: env == "production"
```

## Multiple Conditions

### Using AND Logic
```yaml
- name: AND condition
  debug:
    msg: "Both conditions are true"
  when:
    - condition1 == true
    - condition2 == true
```

### Using OR Logic
```yaml
- name: OR condition
  debug:
    msg: "At least one condition is true"
  when: condition1 == true or condition2 == true
```

## Common Operators

1. Comparison:
   - `==` (equal)
   - `!=` (not equal)
   - `>` (greater than)
   - `<` (less than)
   - `>=` (greater than or equal)
   - `<=` (less than or equal)

2. Logical:
   - `and`
   - `or`
   - `not`

## Testing Variables

### Boolean Tests
```yaml
when: variable | bool
when: is_enabled is true
when: is_disabled is false
```

### String Tests
```yaml
when: version is match("2.*")
when: path is search("/usr/local")
```

### List Tests
```yaml
when: "item in approved_list"
when: users | length > 5
```

## Working with Registered Variables

```yaml
- command: /usr/bin/something
  register: command_result

- debug:
    msg: "Command succeeded"
  when: command_result is succeeded

- debug:
    msg: "Command failed"
  when: command_result is failed
```

## Best Practices

1. Use clear variable names
2. Keep conditions simple and readable
3. Use YAML list syntax for multiple conditions
4. Test conditions with debug tasks first
5. Use registered variables for complex checks

## Common Tests

- `defined`/`undefined`
- `succeeded`/`failed`
- `match`/`search`
- `file`/`directory`
- `version`

## Tips for Testing

1. Use `debug` module to verify conditions
2. Test edge cases
3. Use `-v` flag when running playbooks
4. Check registered variables
5. Test complex conditions in parts

Remember: Always test conditions with `debug` before implementing actual system changes.

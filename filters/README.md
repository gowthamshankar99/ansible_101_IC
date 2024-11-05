# Ansible Filters Guide

This guide demonstrates various filter types and their practical usage in Ansible playbooks and templates.

## 1. Basic String Filters

### 1.1 String Manipulation
```yaml
# String filters example
- name: String filter demonstrations
  hosts: all
  vars:
    example_string: "Hello World"
    file_path: "/path/to/file.txt"
  tasks:
    - name: Display filtered strings
      debug:
        msg:
          - "Uppercase: {{ example_string | upper }}"
          - "Lowercase: {{ example_string | lower }}"
          - "Title Case: {{ example_string | title }}"
          - "Reverse String: {{ example_string | reverse }}"
          - "Replace: {{ example_string | replace('Hello', 'Hi') }}"
          - "Default Value: {{ undefined_var | default('default_value') }}"
          - "Basename: {{ file_path | basename }}"
          - "Dirname: {{ file_path | dirname }}"
```

### 1.2 String Truncating and Formatting
```yaml
- name: String formatting examples
  debug:
    msg:
      - "Truncated: {{ example_string | truncate(5) }}"
      - "Right Pad: {{ example_string | rjust(20) }}"
      - "Left Pad: {{ example_string | ljust(20) }}"
      - "Center: {{ example_string | center(20) }}"
      - "Quote String: {{ example_string | quote }}"
```

## 2. List and Set Filters

### 2.1 List Manipulation
```yaml
- name: List filter demonstrations
  hosts: all
  vars:
    numbers: [3, 1, 4, 1, 5, 9, 2, 6, 5, 3]
    fruits: ['apple', 'banana', 'cherry']
  tasks:
    - name: Display filtered lists
      debug:
        msg:
          - "Unique Items: {{ numbers | unique }}"
          - "Sorted List: {{ numbers | sort }}"
          - "Sorted Unique: {{ numbers | unique | sort }}"
          - "First Item: {{ numbers | first }}"
          - "Last Item: {{ numbers | last }}"
          - "Min Value: {{ numbers | min }}"
          - "Max Value: {{ numbers | max }}"
          - "Random Item: {{ numbers | random }}"
          - "Join Items: {{ fruits | join(', ') }}"
          - "Difference: {{ numbers | difference([1, 2, 3]) }}"
          - "Union: {{ numbers | union([7, 8, 9]) }}"
          - "Intersection: {{ numbers | intersect([1, 3, 5]) }}"
```

### 2.2 List Comprehension
```yaml
- name: List comprehension examples
  vars:
    users: 
      - {name: 'John', age: 25}
      - {name: 'Jane', age: 30}
      - {name: 'Bob', age: 35}
  tasks:
    - name: Extract user names
      debug:
        msg: "User names: {{ users | map(attribute='name') | list }}"

    - name: Filter adult users
      debug:
        msg: "Adults: {{ users | selectattr('age', '>=', 30) | list }}"
```

## 3. Dictionary Filters

### 3.1 Dictionary Manipulation
```yaml
- name: Dictionary filter examples
  vars:
    user_dict:
      name: John
      age: 30
      city: New York
  tasks:
    - name: Display filtered dictionaries
      debug:
        msg:
          - "Dict Keys: {{ user_dict | dict2items }}"
          - "Keys Only: {{ user_dict | list }}"
          - "Values Only: {{ user_dict | dict2items | map(attribute='value') | list }}"
          - "Combined: {{ dict1 | combine(dict2) }}"
          - "JSON String: {{ user_dict | to_json }}"
          - "YAML String: {{ user_dict | to_yaml }}"
```

## 4. Path and File Filters

```yaml
- name: File path filter examples
  vars:
    path: "/var/www/html/index.html"
  tasks:
    - name: Display path components
      debug:
        msg:
          - "Basename: {{ path | basename }}"
          - "Directory: {{ path | dirname }}"
          - "Extension: {{ path | splitext | last }}"
          - "Real Path: {{ path | realpath }}"
          - "Expand User: {{ '~/documents' | expanduser }}"
```

## 5. Type Conversion Filters

```yaml
- name: Type conversion examples
  vars:
    number_string: "123"
    boolean_string: "yes"
    json_string: '{"name": "John", "age": 30}'
  tasks:
    - name: Convert types
      debug:
        msg:
          - "To Integer: {{ number_string | int }}"
          - "To Float: {{ number_string | float }}"
          - "To Boolean: {{ boolean_string | bool }}"
          - "To JSON: {{ user_dict | to_json }}"
          - "From JSON: {{ json_string | from_json }}"
          - "To YAML: {{ user_dict | to_yaml }}"
```

## 6. Regular Expression Filters

```yaml
- name: Regex filter examples
  vars:
    text: "Hello 123 World"
    email: "user@example.com"
  tasks:
    - name: Use regex filters
      debug:
        msg:
          - "Regex Search: {{ text | regex_search('\\d+') }}"
          - "Regex Replace: {{ text | regex_replace('\\d+', 'NUM') }}"
          - "Regex Findall: {{ text | regex_findall('\\w+') }}"
          - "Email Validation: {{ email | regex_search('@.*\\.com$') is not none }}"
```

## 7. Date and Time Filters

```yaml
- name: Date and time filter examples
  tasks:
    - name: Display formatted dates
      debug:
        msg:
          - "Current Time: {{ ansible_date_time.iso8601 }}"
          - "Formatted Date: {{ ansible_date_time.iso8601 | to_datetime('%Y-%m-%d') }}"
          - "Unix Timestamp: {{ ansible_date_time.epoch }}"
          - "Human Time: {{ 3600 | human_to_seconds }}"
```

## 8. Network Filters

```yaml
- name: Network filter examples
  vars:
    ip_addr: "192.168.1.1"
    subnet: "192.168.1.0/24"
  tasks:
    - name: Use network filters
      debug:
        msg:
          - "IP Version: {{ ip_addr | ipv4 }}"
          - "IP Address in Subnet: {{ ip_addr | ipv4(subnet) }}"
          - "Next IP: {{ ip_addr | ipv4('next') }}"
          - "Previous IP: {{ ip_addr | ipv4('previous') }}"
```

## 9. Custom Filters

```python
# filter_plugins/custom_filters.py
def my_custom_filter(value, arg):
    return value * arg

class FilterModule(object):
    def filters(self):
        return {
            'my_multiply': my_custom_filter
        }
```

```yaml
# Using custom filter in playbook
- name: Custom filter example
  debug:
    msg: "Result: {{ 5 | my_multiply(3) }}"
```

## 10. Practical Examples

### 10.1 Configuration File Generation
```yaml
# Template file (config.j2)
{% for service in services | sort %}
[{{ service.name }}]
port = {{ service.port | default(8080) }}
host = {{ service.host | default('localhost') }}
enabled = {{ service.enabled | default(true) | bool | lower }}
{% endfor %}

# Playbook
- name: Generate configuration
  template:
    src: config.j2
    dest: /etc/services.conf
  vars:
    services:
      - name: web
        port: 80
        host: web.example.com
      - name: api
        port: 8081
        enabled: false
```

### 10.2 Log Processing
```yaml
- name: Process log files
  vars:
    log_content: "{{ lookup('file', '/var/log/app.log') }}"
  tasks:
    - name: Extract error messages
      debug:
        msg: "Errors: {{ log_content | regex_findall('ERROR.*') }}"

    - name: Count unique IP addresses
      debug:
        msg: "Unique IPs: {{ log_content | regex_findall('\\d+\\.\\d+\\.\\d+\\.\\d+') | unique | length }}"
```

### 10.3 User Data Processing
```yaml
- name: Process user data
  vars:
    users:
      - {name: 'John', age: 25, active: true}
      - {name: 'Jane', age: 30, active: false}
      - {name: 'Bob', age: 35, active: true}
  tasks:
    - name: Get active adult users
      debug:
        msg: "Active adults: {{ users | selectattr('active') | selectattr('age', '>=', 30) | map(attribute='name') | list }}"
```

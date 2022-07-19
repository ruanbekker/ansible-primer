# Docs and Useful Notes

## Conditionals

### Role conditional within a Playbook

Conditional in a playbook, to only run the role when a specific variable equals to something:

```yaml
# playbooks/playbook.yml
- name: deploys node exporter
  hosts: all
  become: yes
  become_user: root
  vars:
    app_user: "prometheus"
    app_group: "prometheus"
    node_exporter_version: "1.3.1"
  roles:
    - { role: ../roles/create-os-user }
    - { role: ../roles/prometheus-node-exporter, when: skip_installation == "false" }
```

Then the default vars:

```yaml
# roles/prometheus-node-exporter/defaults/main.yml
skip_installation == "false"
```

And the host var:

```yaml
# host_vars/demo.example.com/vars
skip_installation: "true"
```

## Jinja2 

### if then else

```
      {% if environment_name == 'development' %}
        job: "dev/{{ service_name }}-logs"
      {% elif environment_name == 'production' %}
        job: "prod/{{ service_name }}-logs"
      {% else %}
        job: "{{ environment_name }}/{{ service_name }}-logs"
      {% endif %}
``` 

### using and operator

```
{% if app_name == 'demo' and app_env == 'dev' %}
  - job_name: demo-app-logs
    static_configs:
    - targets:
        - localhost
{% endif %}
```

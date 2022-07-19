# jinja2 templates 


## if then else

```
      {% if environment_name == 'development' %}
        job: "dev/{{ service_name }}-logs"
      {% elif environment_name == 'production' %}
        job: "prod/{{ service_name }}-logs"
      {% else %}
        job: "{{ environment_name }}/{{ service_name }}-logs"
      {% endif %}
``` 

## using and operator

```
{% if app_name == 'demo' and app_env == 'dev' %}
  - job_name: demo-app-logs
    static_configs:
    - targets:
        - localhost
{% endif %}
```

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

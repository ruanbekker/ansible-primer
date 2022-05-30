# ansible-primer
Ansible Primer Repo

## About

This is my ansible primer repository that I've put together to reference when I need it.

## What does it do

This example includes a ansible role called "promtail-for-linux" wich installs the [grafana promtail](https://grafana.com/docs/loki/latest/clients/promtail/) agent on 3 nodes, which is grouped into 3 groups:

- `[prod]    prod.example.com`
- `[staging] staging.example.com`
- `[dev]     dev.example.com`

It requires a [Grafana Loki](https://grafana.com/oss/loki/) server, but it's beyond the scope of this example.

## Project Structure

The directory structure generated with `tree`:

```bash
.
├── EXTRAS.md
├── README.md
├── ansible.cfg
├── group_vars
│   ├── dev
│   │   └── vars
│   ├── prod
│   │   └── vars
│   └── staging
│       └── vars
├── host_vars
│   ├── dev.example.com
│   │   └── vars
│   ├── prod.example.com
│   │   └── vars
│   └── staging.example.com
│       └── vars
├── hosts
├── playbooks
│   └── promtail.yml
└── roles
    └── promtail-for-linux
        ├── defaults
        │   └── main.yml
        ├── files
        │   └── promtail.service
        ├── handlers
        │   └── main.yml
        ├── tasks
        │   ├── main.yml
        │   └── task-ubuntu.yml
        ├── templates
        │   └── linux-promtail-config.yml.j2
        └── vars
            └── main.yml

17 directories, 18 files
```

## Usefil Tips

Variable Precendence (only the ones I'm interested in - see the docs at the bottom for a detailed overview):

```
1. Role defaults (<role-name>/defaults/main.yml) 
2. Inventory file or script group vars
3. Playbook group_vars
4. Playbook host_vars
5. Role vars (defined in <role-name>/vars/main.yml)
6. Task vars (only for the task)
7. Extra vars (-e "foo=bar") (always win precedence)
```

## Useful Documentation

- [Variable Precedence](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable)
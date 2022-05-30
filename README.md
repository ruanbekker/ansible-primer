# ansible-primer
Ansible Primer Repo

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
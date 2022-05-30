## Infrastructure

### Multipass

To provision 3 nodes with multipass, create this file (`cloud-init.yml`) in your current working directory:

```yaml
#cloud-config
groups:
  - admins

users:
  - default
  - name: ubuntu
    groups: admins
    sudo:  ALL=(ALL) NOPASSWD:ALL
    ssh_authorized_keys:
      - __SSH_PUBLIC_KEY__

package_update: true

packages:
 - apt-transport-https
 - gnupg
 - ca-certificates
 - wget
 - curl
 - apt-utils
 - net-tools
 - python3
```

Then to include your default ssh public key (change where needed), for mac:

```bash
sed -i '' "s|__SSH_PUBLIC_KEY__|$(cat ~/.ssh/id_rsa.pub)|g" cloud-init.yml
```

For linux:

```bash
sed -i "s|__SSH_PUBLIC_KEY__|$(cat ~/.ssh/id_rsa.pub)|g" cloud-init.yml
```

Then launch the vms:

```bash
for vm in dev staging prod; do multipass launch --name $vm --cpus 1 --mem 512M --disk 8G --cloud-init cloud-init.yml focal; done
```

Then list your vms:

```bash
multipass list
Name                    State             IPv4             Image
dev                     Running           192.168.64.3     Ubuntu 20.04 LTS
prod                    Running           192.168.64.32    Ubuntu 20.04 LTS
staging                 Running           192.168.64.31    Ubuntu 20.04 LTS
```

## Ansible

### Verify Connectivity

Check if ansible can reach all vms:

```bash
ansible all -m ping 

staging.example.com | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
dev.example.com | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
prod.example.com | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

Or targeting a group:

```bash
ansible prod -m ping   

prod.example.com | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

Or targeting a host:

```bash
ansible prod.example.com -m ping

prod.example.com | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

To retrieve all the metadata or ansible facts of a node, in this case everything from the "prod" group, but just return "prod.example.com" (we only have the one host under that group, but this is just for demonstration):

```bash
ansible prod -m gather_facts --limit prod.example.com

prod.example.com | SUCCESS => {
    "ansible_facts": {
        "ansible_all_ipv4_addresses": [
            "192.168.64.32"
        ],
        ...
```

Run playbooks:

```
ansible-playbook playbooks/promtail.yml
```

We can see that the dev and prod vars was referenced, for prod:

```bash
ssh ubuntu@192.168.64.32 cat /etc/promtail/promtail.yml

server:
  http_listen_port: 9080
  grpc_listen_port: 0

positions:
  filename: /var/lib/promtail/positions.yaml

clients:
  - url: https://admin:admin@loki-api.example.com:443/loki/api/v1/push

scrape_configs:
  - job_name: journal
    journal:
      json: false
      max_age: 12h
      path: /var/log/journal
      labels:
        job: systemd-logs
        tenant: prod.example.com
        name: prod.example.com
        env: prod
    relabel_configs:
      - source_labels: ["__journal__systemd_unit"]
        target_label: "unit"

```

and for dev:

```bash
ssh ubuntu@192.168.64.3 cat /etc/promtail/promtail.yml 

server:
  http_listen_port: 9080
  grpc_listen_port: 0

positions:
  filename: /var/lib/promtail/positions.yaml

clients:
  - url: http://localhost:3000/loki/api/v1/push

scrape_configs:
  - job_name: journal
    journal:
      json: false
      max_age: 12h
      path: /var/log/journal
      labels:
        job: systemd-logs
        tenant: dev.example.com
        name: dev.example.com
        env: dev
    relabel_configs:
      - source_labels: ["__journal__systemd_unit"]
        target_label: "unit"
```

Delete the vms:

```bash
for vm in dev staging prod; do multipass delete $vm --purge ; done
```
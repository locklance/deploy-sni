# Automatic SNI deployment on multiply servers
## Preparation
### 1. Add ssh keys to ssh-agent
To use ansible you need to own ssh keys for remote servers and add them to ssh-agent, by using:
```sh
ssh-add .ssh/your_key
```

### 2. Create venv
You also need to create venv:
```sh
python3 -m venv venv
source venv/bin/activate
```

### 3. Install Ansible
Install Ansible:
```sh
pip install ansible
```

### 4. Install additional roles
Install role for docker installation:
```sh
ansible-galaxy install geerlingguy.docker
```

## Check Ansible configs
**Check ansible inventory:**
```sh
ansible-inventory -i ansible/inventory.yml --list
```

**Ping servers in inventory:**
`ansible vpnnodes -m ping -i ansible/inventory.yml`
you can use any of group, hosts etc instead `vpnnodes`. 

## SNI deployment
1. Create `secrets.yml` with `CF_API_TOKEN` inside sni role:
```sh
└── roles
    └── sni
        ├── files
        │   └── secrets.yml <---- PUT CF_API_TOKEN HERE
        ├── tasks
        │   └── main.yml
        └── templates
            └── env.j2
```

```sh
# secrets.yml example
CF_API_TOKEN: your_api_token
```

2. Start Ansible script:
```sh
ansible-playbook ansible/playbooks/deploy_sni.yml -i ansible/inventory.yml -l finland
```
> ** Use `--ask-become-pass` option to enter sudo if not working **
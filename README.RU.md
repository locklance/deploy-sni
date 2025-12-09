# Разворачиваем SNI на куче серверов в два клика
Данный проект предназначен для автоматизации процесса запуска SNI-заглушек с сертификатом DNS-01 от Cloudflare, который позволяет запускать сразу несколько SNI-заглушек на разных серверах под одним доменом!
Запущенные таким образом на одном домене, но на разных серверах SNI-заглушки позволяют в полной мере безопасно использовать DNS-балансировку.

## Подготовка
### 1. Добавляем ssh-ключи на своем ПК через ssh-agent
Для безопасности, чтобы не добавлять путь до каждого ключа в inventory файле, добавляем ssh ключ в систему через ssh-add:
```sh
ssh-add .ssh/your_key
```
### 2. Создаем venv
```sh
python3 -m venv venv
source venv/bin/activate
```
### 3. Устанавливаем Ansible
```sh
pip install ansible
```
### 4. Устанавливаем роль Ansible
Устанавливаем роль для автоматической загрузки docker в систему:
```sh
ansible-galaxy install geerlingguy.docker
```
## Проверка
**Проверка правильности файла Ansible inventory:**
```sh
ansible-inventory -i ansible/inventory.yml --list
```
**Пингуем все сервера в Ansible:**
`ansible vpnnodes -m ping -i ansible/inventory.yml`
вместо `vpnnodes`, можем использовать любую другую группу или отдельную страну (например `finland`, `FL_VPN_NODE_1`). 
## Запуск SNI
1. Создаем секрет `secrets.yml` с API-ключом Cloudflare `CF_API_TOKEN`:
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

2. Запускаем Ansible скрипт:
```sh
ansible-playbook ansible/playbooks/deploy_sni.yml -i ansible/inventory.yml -l finland
```
> ** Используйте опцию `--ask-become-pass` для исполнения sudo команд на сервере если не сработало без неё **
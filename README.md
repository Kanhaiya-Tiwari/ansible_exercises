

# 1️⃣ What Ansible Really Is

Ansible is **NOT** a scripting tool.
It is an **Infrastructure Automation Engine**.

It means:

> You write **what you want**, Ansible decides **how to do it**.

Example:
You write:

```
nginx should be installed
```

Ansible figures out:

* yum or apt
* already installed or not
* restart or not

This is called **idempotency**.

---

# 2️⃣ Architecture (How Ansible Works)

Ansible has only **two types of machines**:

| Type         | Meaning                    |
| ------------ | -------------------------- |
| Control Node | Where Ansible is installed |
| Managed Node | Servers being controlled   |

No agent, no daemon, no background service.

Ansible connects using:

```
SSH
```

That’s why it is called **agentless**.

---

# 3️⃣ Installation

### On Ubuntu

```
sudo apt update
sudo apt install ansible -y
```

### On RHEL / Amazon Linux

```
sudo dnf install ansible-core -y
```

Check:

```
ansible --version
```

---

# 4️⃣ Inventory (The Heart of Ansible)

Inventory = list of servers

Example:

```
inventory/hosts
```

```ini
[web]
13.233.45.120

[db]
13.234.78.221
```

You are telling Ansible:

* these IPs exist
* these groups exist

Test:

```
ansible all -i inventory/hosts -m ping
```

If ping works → SSH + Python is working.

---

# 5️⃣ Ansible Ad-Hoc Commands

These are one-line commands.

### Check uptime

```
ansible all -m command -a "uptime"
```

### Install package

```
ansible web -m apt -a "name=nginx state=present" --become
```

### Restart service

```
ansible web -m service -a "name=nginx state=restart" --become
```

Ad-hoc = quick tasks
Playbooks = real automation

---

# 6️⃣ Playbooks

Playbook = YAML file

This is where real automation lives.

Example:

```
install_nginx.yml
```

```yaml
- name: Install nginx
  hosts: web
  become: yes

  tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: present

    - name: Start nginx
      service:
        name: nginx
        state: started
```

Run:

```
ansible-playbook install_nginx.yml
```

---

# 7️⃣ Tasks

Task = single action

Example:

```
Install nginx
Start nginx
Copy file
Create user
Restart service
```

Each task uses a **module**.

---

# 8️⃣ Modules (Ansible Power)

Modules are built-in functions.

| Module  | Work                |
| ------- | ------------------- |
| apt     | Install packages    |
| yum     | Install packages    |
| copy    | Copy files          |
| file    | Create dirs         |
| service | Start/stop services |
| user    | Create users        |
| shell   | Run shell           |
| command | Run command         |
| git     | Clone repo          |

Example:

```
- name: Copy config
  copy:
    src: app.conf
    dest: /etc/app.conf
```

---

# 9️⃣ Variables

Variables make playbooks dynamic.

```
vars:
  app_port: 8080
```

Use:

```
{{ app_port }}
```

Example:

```
port={{ app_port }}
```

---

# 10️⃣ Handlers

Handlers run only when something changes.

Example:

```
notify: restart nginx
```

```
handlers:
  - name: restart nginx
    service:
      name: nginx
      state: restarted
```

---

# 11️⃣ ansible.cfg

This is the brain of Ansible.

Example:

```
[defaults]
inventory=inventory/hosts
remote_user=ubuntu
private_key_file=kanha.pem
host_key_checking=False
```

This avoids writing:

```
-i inventory/hosts
-u ubuntu
--key-file
```

every time.

---

# 12️⃣ Roles

Roles = reusable automation

Structure:

```
roles/nginx/tasks/main.yml
roles/nginx/files/
roles/nginx/templates/
```

Used in playbook:

```
roles:
  - nginx
```

This is how companies manage 100s of playbooks.

---

# 13️⃣ Templates (Jinja2)

Dynamic config files.

Example:

```
server {{ server_name }};
listen {{ port }};
```

Used by:

```
template:
  src: nginx.conf.j2
  dest: /etc/nginx/nginx.conf
```

---

# 14️⃣ Loops

```
with_items:
  - git
  - docker
  - nginx
```

---

# 15️⃣ Conditionals

```
when: ansible_os_family == "Debian"
```

---

# 16️⃣ Facts

```
ansible_facts
```

Check:

```
ansible all -m setup
```

Used for OS, IP, memory, etc.


# 17️⃣ Vault (Secrets)

Encrypt passwords.

```
ansible-vault create secret.yml
ansible-vault edit secret.yml
ansible-playbook playbook.yml --ask-vault-pass
# 18️⃣ Real-World Usage

Companies use Ansible to:

* Build servers
* Configure Docker
* Deploy apps
* Patch OS
* Setup Kubernetes
* Configure monitoring

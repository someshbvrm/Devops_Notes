
---

# 🚀 Ansible Notes

Ansible is an **open-source automation tool** that helps in **configuration management, application deployment, and orchestration**.
👉 It’s **agentless** (no software needed on managed nodes) and uses **SSH + YAML** for automation.

---

## 🏗️ Ansible Architecture (In a Nutshell)

👉 Ansible follows a **controller–node model**:

* **Control Node (Controller)** 🖥️

  * Where Ansible is installed.
  * Runs playbooks and sends tasks to managed nodes.

* **Managed Nodes (Targets)** 🖧

  * The servers Ansible manages.
  * No agent required — just **SSH** and **Python**.

* **Inventory 📋**

  * A list of target servers (IPs/hostnames).

* **Modules ⚙️**

  * Small programs Ansible executes on nodes (e.g., install package, copy file).

* **Playbooks 📖**

  * YAML-based “recipes” describing automation tasks.

* **Plugins 🔌**

  * Extend functionality (connections, logging, cache, etc.).

* **Roles 📦**

  * Structured way to organize playbooks, vars, handlers, templates, etc.

⚡ This **push-based, agentless architecture** makes Ansible **lightweight, scalable, and secure**.

---

## 🔑 Setting Up Passwordless SSH

On **controller (Ansible host)**:

```bash
ssh-keygen   # generates key (default: ~/.ssh/id_rsa.pub)
cat ~/.ssh/id_rsa.pub
```

On **managed node**:

```bash
cd ~/.ssh
vi authorized_keys   # paste public key here
```

✅ Now controller can connect without password:

```bash
ssh user@public-ip
```

---

## ⚡ Installing Ansible (Ubuntu Example)

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install ansible -y
```

Create workspace:

```bash
mkdir ~/ansibleplaybooks && cd ~/ansibleplaybooks
```

---

## 📋 Inventory File Example (`hosts`)

```ini
[webservers]
3.88.234.162
3.88.234.166
3.88.234.168

[dbservers]
3.88.234.167
```

✅ Test connection:

```bash
ansible all -m ping -i hosts
ansible webservers -m ping -i hosts
```

---

## ⚡ Ad-Hoc Commands

👉 **Syntax**:

```bash
ansible <host-pattern> -m <module> -a "<args>" -i hosts
```

### 🔍 System Checks

```bash
ansible all -m ping -i hosts
ansible all -m shell -a "uptime" -i hosts
ansible all -m shell -a "df -h" -i hosts
ansible all -m shell -a "free -m" -i hosts
```

### 📂 File & Directory

```bash
ansible all -m copy -a "src=/etc/hosts dest=/tmp/hosts" -i hosts
ansible all -m file -a "path=/tmp/testdir state=directory" -i hosts
ansible all -m file -a "path=/tmp/testfile state=absent" -i hosts
```

### 📦 Packages

```bash
ansible all -m yum -a "name=httpd state=present" -i hosts --become
ansible all -m apt -a "name=nginx state=present" -i hosts --become
```

### 🔧 Services

```bash
ansible all -m service -a "name=httpd state=started" -i hosts --become
ansible all -m service -a "name=nginx state=restarted" -i hosts --become
```

### 👤 Users

```bash
ansible all -m user -a "name=devops state=present" -i hosts --become
ansible all -m user -a "name=devops state=absent" -i hosts --become
```

📖 Ref: [Ansible Ad-Hoc Docs](https://docs.ansible.com/ansible/latest/command_guide/intro_adhoc.html)

---

## 📖 Playbooks

YAML files that describe tasks step by step.

### ✅ Example: Install Nginx

```yaml
---
- hosts: web
  become: yes
  tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: latest

    - name: Start nginx
      service:
        name: nginx
        state: started
```

Run:

```bash
ansible-playbook nginx.yaml --syntax-check -i hosts
ansible-playbook nginx.yaml --list-hosts -i hosts
ansible-playbook nginx.yaml -i hosts
```

---

## 🔄 Handlers & Notify

Avoid restarting services unnecessarily.

```yaml
---
- name: Install and Configure Nginx
  hosts: web
  become: yes
  tasks:
    - name: Install Nginx
      apt:
        name: nginx
        state: present
      notify: Restart Nginx

    - name: Copy Config
      template:
        src: nginx.conf.j2
        dest: /etc/nginx/nginx.conf
      notify: Reload Nginx

  handlers:
    - name: Restart Nginx
      service:
        name: nginx
        state: restarted

    - name: Reload Nginx
      service:
        name: nginx
        state: reloaded
```

---

## ⚡ Best Practices

✅ Use **roles** for reusable structure.
✅ Always `--syntax-check` before running.
✅ Use **gather\_facts: no** if facts are not needed.
✅ Use **tags** for selective execution.
✅ Prefer **idempotent modules** (don’t use `command`/`shell` unless required).

---

## 📝 Quick Interview Notes (Bullet Form)

* **Controller Node 🖥️**: Where Ansible runs.
* **Managed Nodes 🖧**: Agentless, only SSH required.
* **Inventory 📋**: List of hosts.
* **Modules ⚙️**: Reusable tasks (Python).
* **Playbooks 📖**: YAML automation recipes.
* **Roles 📦**: Structured playbooks.
* **Handlers 🔄**: Triggered by `notify`.
* **Push-based** model, **idempotent execution**.

---

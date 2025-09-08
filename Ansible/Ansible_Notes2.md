
---

# 📘 Advanced Ansible Notes

Ansible is widely used for **continuous deployment (CD)**, **infrastructure automation**, and **Linux hardening**.
We use **Terraform** for provisioning infrastructure ☁️, and **Ansible** for **application deployment & configuration**.

---

## ⚡ Conditional Package Installation (Apache HTTP Server)

```yaml
---
- name: Install Apache HTTPD based on OS family
  hosts: your_target_hosts
  become: yes

  tasks:
    - name: Install Apache2 on Debian/Ubuntu 🐧
      ansible.builtin.apt:
        name: apache2
        state: present
        update_cache: yes
      when: ansible_os_family == "Debian"

    - name: Install httpd on RedHat/CentOS 🎩
      ansible.builtin.yum:
        name: httpd
        state: present
      when: ansible_os_family == "RedHat"
```

📝 *Here Ansible gathers host facts automatically. Based on `ansible_os_family`, only the relevant task runs.*

---

## 📦 Installing Multiple Packages at Once

```yaml
---
- name: Install multiple packages
  hosts: your_target_hosts
  become: yes

  tasks:
    - name: Install packages in bulk
      ansible.builtin.package:
        name:
          - git
          - curl
          - vim
        state: present
```

---

## 🔁 Installing Packages in a Loop

```yaml
---
- name: Install packages with loop
  hosts: your_target_hosts
  become: true

  tasks:
    - name: Install packages iteratively
      ansible.builtin.package:
        name: "{{ item }}"
        state: present
      loop:
        - git
        - wget
        - unzip
```

💡 Useful when package names are dynamic or passed as variables.

---

## 🚀 Application Deployment via Ansible

We commonly deploy:

* **Frontend** → Apache HTTPD / Nginx
* **Backend** → Tomcat
* **Databases** → MySQL, PostgreSQL, NoSQL (MongoDB, Cassandra)
* **Message Queues** → RabbitMQ

---

## 🐱 Tomcat Server + WAR Deployment

```yaml
---
- name: Install Tomcat and deploy WAR
  hosts: tomcatservers
  become: yes

  tasks:
    - name: Install Java ☕
      ansible.builtin.apt:
        name: openjdk-11-jdk
        state: present
        update_cache: yes

    - name: Download and Extract Tomcat 📦
      ansible.builtin.unarchive:
        src: https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.85/bin/apache-tomcat-9.0.85.tar.gz
        dest: /opt
        remote_src: yes
      when: ansible_os_family == "Debian"

    - name: Deploy WAR file 🚀
      ansible.builtin.copy:
        src: /path/to/app.war
        dest: /opt/apache-tomcat-9.0.85/webapps/

    - name: Restart Tomcat Service 🔄
      ansible.builtin.systemd:
        name: tomcat
        enabled: yes
        state: restarted
```

---

## 🏷️ Running Specific Tasks with Tags

```bash
ansible-playbook deploy.yml --tags "packages,configurations"
```

📖 Docs: [Playbook Tags](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_tags.html)

---

## 🔐 Securing Playbooks with Vaults

```bash
# Encrypt files
ansible-vault encrypt secrets.yml

# Decrypt files
ansible-vault decrypt secrets.yml

# Run playbook with vault
ansible-playbook site.yml --ask-vault-pass
```

---

## 🌐 Inventory with Host & Group Variables

```ini
[webservers]
web01.example.com http_port=8080
web02.example.com http_port=8081

[dbservers]
db01.example.com db_port=3306

[texas:vars]
ntp_server=ntp.texas.example.com
proxy=proxy.texas.example.com
```

📌 Use in playbooks:

```yaml
- name: Print port
  debug:
    msg: "Running on port {{ http_port }}"
```

---

## 📥 External Variables & Extra Vars

```yaml
---
- hosts: all
  vars_files:
    - vars/external_vars.yml
  tasks:
    - name: Show favorite color
      debug:
        msg: "My favorite color is {{ favcolor }}"
```

👉 Pass from CLI:

```bash
ansible-playbook deploy.yml -e "version=1.2.3 env=prod"
```

---

## 🔑 Variable Precedence (Low → High Priority)

1. **Role defaults**
2. **Inventory group vars**
3. **Inventory host vars**
4. **Play vars / vars\_files**
5. **Role vars**
6. **Block vars**
7. **Task vars**
8. **Registered vars / set\_fact**
9. **Role params / include params**
10. **Extra vars (`-e`) → Always win 🏆**

---

## 📦 Roles in Ansible

A **role** = predefined structure for playbooks, vars, handlers, templates, etc.

```
my_role/
├── defaults/   # Lowest priority vars
├── vars/       # Higher priority vars
├── tasks/      # Main tasks
├── handlers/   # Handlers
├── templates/  # Jinja2 templates
├── files/      # Static files
├── meta/       # Role dependencies
└── tests/      # Test playbooks
```

👉 Example usage:

```yaml
- hosts: webservers
  roles:
    - common
    - { role: nginx, port: 80 }
```

👉 Create new role:

```bash
ansible-galaxy role init my_role
```

---

## 🛡️ Linux Hardening with Ansible

Typical weekend activity 🕒:

* 🔄 Patching & updates
* 🗑️ Cleaning unnecessary files/users
* 🔒 Restricting privileges
* 🚫 Stopping unwanted services

### Example Hardening Playbook

```yaml
---
- name: Harden Linux server
  hosts: linux_servers
  become: yes
  gather_facts: yes

  tasks:
    - name: Ensure firewall installed 🔥
      ansible.builtin.dnf:
        name: firewalld
        state: present

    - name: Enable firewall service
      ansible.builtin.service:
        name: firewalld
        state: started
        enabled: yes

    - name: Block non-required services
      ansible.posix.firewalld:
        service: "{{ item }}"
        state: disabled
        permanent: yes
        immediate: yes
      loop:
        - cockpit
        - dhcpv6-client

    - name: Enable SSH
      ansible.posix.firewalld:
        service: ssh
        state: enabled
        permanent: yes
        immediate: yes

    - name: Ensure SELinux is enforcing 🔐
      ansible.posix.selinux:
        policy: targeted
        state: enforcing
```

---

## 📝 Summary for Interviews

* **Conditional execution** → `when: ansible_os_family`
* **Loops** → `loop:` for repetitive tasks
* **Tags** → Run only specific tasks
* **Vaults** → Encrypt secrets (passwords, keys, certs)
* **Variables** → host\_vars, group\_vars, extra vars, precedence order
* **Roles** → Standard reusable structure
* **Hardening** → Patching, firewall, SELinux, sysctl, auditd

---

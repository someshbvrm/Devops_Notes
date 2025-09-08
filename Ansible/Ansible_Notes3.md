
---

# ⚡ Ansible Tags & Variables Cheatsheet

Ansible provides **tags** and **variables** to control execution and make playbooks reusable, dynamic, and flexible 🚀.

---

## 🏷️ Ansible Tags

Tags allow you to **selectively run, skip, or always execute** specific tasks or plays.

| 📝 **Concept**            | 💻 **Example / Usage**                                                                         | 🔍 **Notes**                                          |
| ------------------------- | ---------------------------------------------------------------------------------------------- | ----------------------------------------------------- |
| **Tag a task**            | `yaml<br>- name: Install Apache<br>  apt: name=apache2 state=present<br>  tags: webserver<br>` | Add tags to individual tasks                          |
| **Tag multiple tasks**    | `yaml<br>- name: Install PHP<br>  apt: name=php state=present<br>  tags: [webserver, php]<br>` | Use a list when assigning multiple tags               |
| **Tag a play**            | `yaml<br>- hosts: all<br>  tags: always<br>  tasks: ...`                                       | Applies tags to **all tasks** in the play             |
| **Run tagged tasks**      | `bash<br>ansible-playbook site.yml --tags "webserver"<br>`                                     | Runs only tasks with `webserver` tag                  |
| **Skip tagged tasks**     | `bash<br>ansible-playbook site.yml --skip-tags "database"<br>`                                 | Skips tasks with `database` tag                       |
| **Special tag: always**   | `yaml<br>tags: always<br>`                                                                     | Runs **every time**, even if other tags are specified |
| **Special tag: never**    | `yaml<br>tags: never<br>`                                                                      | Skips unless explicitly called with `--tags never`    |
| **Mixing tags & skip**    | `bash<br>ansible-playbook site.yml --tags "webserver,setup" --skip-tags "php"<br>`             | Run multiple tags while skipping others               |
| **List tags in playbook** | `bash<br>ansible-playbook site.yml --list-tags<br>`                                            | Shows available tags in a playbook                    |

👉 **Best Practice**: Tag logically (`setup`, `webserver`, `database`, `security`, `deploy`) to simplify selective runs.

---

## 🧩 Ansible Variables

Variables make playbooks **dynamic** by storing values that can be reused across tasks, roles, and templates.

### 🔑 Types of Variables

| 📂 **Variable Type**     | 💡 **Definition / Example**                                        |
| ------------------------ | ------------------------------------------------------------------ |
| **Playbook / Task vars** | Defined under `vars:` in playbooks or tasks                        |
| **Host vars**            | Defined in inventory or `host_vars/hostname.yml`                   |
| **Group vars**           | Defined in `group_vars/groupname.yml`                              |
| **Facts variables**      | Auto-gathered system info (`ansible_facts`)                        |
| **Extra vars**           | Passed at runtime via CLI `-e "var=value"` (highest priority)      |
| **Registered vars**      | Store task output for later reuse (`register: varname`)            |
| **Defaults**             | Defined in `defaults/main.yml` (lowest priority, safe to override) |
| **set\_fact**            | Define variables dynamically at runtime (`set_fact: var=value`)    |

---

### 📝 Examples

#### 1️⃣ Playbook Variables

```yaml
- hosts: all
  vars:
    apache_port: 80

  tasks:
    - name: Install Apache
      apt:
        name: apache2
        state: present

    - name: Start Apache service
      service:
        name: apache2
        state: started
      notify: restart apache

  handlers:
    - name: restart apache
      service:
        name: apache2
        state: restarted
```

---

#### 2️⃣ Host Variables (Inventory)

```ini
[web]
web1 ansible_host=192.168.1.10 apache_port=8080
```

---

#### 3️⃣ Extra Variables (CLI)

```bash
ansible-playbook playbook.yml -e "apache_port=8081"
```

---

#### 4️⃣ Registered Variables

```yaml
- name: Check Apache status
  command: systemctl status apache2
  register: apache_status

- name: Show Apache status
  debug:
    var: apache_status.stdout
```

---

### 🎯 Accessing Variables in Templates / Tasks

```yaml
- debug:
    msg: "Apache is running on port {{ apache_port }}"
```

---

### 🏆 Variable Precedence (Highest → Lowest)

1. **Extra vars** (`-e`)
2. **Task vars**
3. **Block vars**
4. **Role vars**
5. **Play vars**
6. **Host vars**
7. **Group vars**
8. **Facts**
9. **Role defaults**

⚠️ Always remember: `-e` (extra vars) override everything!

---

✨ With tags you **control execution**, with variables you **control flexibility**. Together, they make your playbooks **powerful & reusable** 🔥.

---


---

# 📘 Ansible Architecture – Notes

## 🔹 1. Overview

* **Ansible** is an **open-source IT automation tool**.
* Used for **configuration management, application deployment, orchestration, and provisioning**.
* Designed to be **simple, agentless, and secure**.
* Uses **YAML (Playbooks)** to describe automation tasks.

---

## 🔹 2. Core Components

### 1. **Control Node (Ansible Controller)**

* Central machine where **Ansible is installed**.
* Executes automation tasks.
* Users run **ad-hoc commands** or **playbooks** from here.
* Needs **Python + Ansible installed**.
* Can manage hundreds/thousands of nodes.

---

### 2. **Managed Nodes (Inventory Hosts / Targets)**

* The remote machines that Ansible manages.
* Do **not** need Ansible installed.
* Requirements:

  * **Python** installed (most Linux distros already have it).
  * **SSH access** from the control node.
* Defined in an **inventory file** (`/etc/ansible/hosts` or custom `.ini` / `.yaml`).

---

### 3. **Inventory**

* List of hosts and groups that Ansible manages.
* Types:

  * **Static Inventory** → manually defined in `.ini` or `.yaml`.
  * **Dynamic Inventory** → fetched from cloud providers (AWS, Azure, GCP, etc.).
* Example (INI format):

  ```ini
  [web]
  web1.example.com
  web2.example.com

  [db]
  db1.example.com
  ```

---

### 4. **Modules**

* Small programs that perform specific tasks (copy files, install packages, create users, etc.).
* Run on managed nodes via SSH.
* Example: `yum`, `apt`, `copy`, `service`, `user`.

---

### 5. **Plugins**

* Extend Ansible’s functionality.
* Types: Connection plugins (SSH, WinRM), Callback plugins, Cache plugins, etc.

---

### 6. **Playbooks**

* Written in **YAML**.
* Describe automation workflows (set of tasks to run on managed nodes).
* Example:

  ```yaml
  - hosts: web
    tasks:
      - name: Install nginx
        apt:
          name: nginx
          state: present
  ```

---

### 7. **Ad-hoc Commands**

* Quick one-liner commands without playbooks.
* Example:

  ```bash
  ansible all -m ping
  ansible web -m apt -a "name=nginx state=present"
  ```

---

## 🔹 3. Execution Flow

1. Admin writes **Playbooks** or runs **ad-hoc commands** from the **Control Node**.
2. Ansible reads the **Inventory** to find target nodes.
3. Establishes connection to managed nodes (default: **SSH**).
4. Executes **modules** (via Python) on the remote machines.
5. Collects output and returns results to the Control Node.

---

## 🔹 4. Architecture Diagram (Text Form)

```
         +-------------------+
         |  Control Node     |
         |  (Ansible Engine) |
         +-------------------+
                |
         SSH / WinRM
                |
   --------------------------------
   |              |               |
+---------+   +---------+    +---------+
| Managed |   | Managed |    | Managed |
| Node 1  |   | Node 2  |    | Node 3  |
+---------+   +---------+    +---------+
```

---

## 🔹 5. Key Characteristics

* **Agentless**: No need to install agents on managed nodes.
* **Push-based**: Control node pushes configurations over SSH.
* **Idempotent**: Ensures same results even if playbooks run multiple times.
* **Extensible**: Supports plugins, custom modules, and dynamic inventories.

---

✅ **In summary**:

* **Control Node** = Brain (runs Ansible).
* **Inventory** = List of machines.
* **Managed Nodes** = Targets (need only SSH + Python).
* **Playbooks** = Automation instructions.
* **Modules** = Actual workers.

---

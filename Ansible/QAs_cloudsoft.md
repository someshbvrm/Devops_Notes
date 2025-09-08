
---
# 🔧 Ansible Interview Questions & Answers

## 📘 Ansible Basics & Core Concepts

### 1. What is Ansible, and how does it differ from other configuration management tools?
**A:**  
Ansible is an **open-source automation tool** used for configuration management, application deployment, and orchestration. It uses **YAML** for playbooks and requires **no agents** on target nodes (uses SSH).

**Key Differences:**
- **Agentless** vs Puppet/Chef (agent-based)
- **YAML-based** vs Ruby (Chef) or custom DSL (Puppet)
- **Easy to learn** and human-readable
- **Push-based** architecture

```ascii
+-----------------+       +-----------------+
|   Ansible       | SSH   |   Target        |
|   Control Node  +------->   Hosts         |
|   (Push)        |       |   (No Agent)    |
+-----------------+       +-----------------+
```

---

### 2. Explain the difference between Ad-hoc commands and Playbooks.
**A:**  
- **Ad-hoc commands:** One-off tasks using `ansible` CLI.  
  Example:  
  ```bash
  ansible all -m ping
  ```
- **Playbooks:** Reusable, structured YAML files for complex workflows.  
  Example:  
  ```yaml
  - hosts: all
    tasks:
      - name: Ping hosts
        ping:
  ```

---

### 3. What are Inventory files in Ansible?
**A:**  
Inventory files define hosts and groups. Can be INI or YAML.

**Example (INI):**
```ini
[web]
web1.example.com
web2.example.com

[db]
db1.example.com
```

**Dynamic Inventory:** Uses scripts (e.g., AWS EC2, Azure) to generate inventory dynamically.

---

### 4. Static vs Dynamic Inventory
**Static:**  
- Manual list of hosts.  
- Good for small, fixed environments.

**Dynamic:**  
- Auto-generated from cloud providers.  
- Ideal for auto-scaling groups.

---

### 5. Roles vs Playbooks
**Playbook:** Single YAML file with tasks.  
**Role:** Reusable structure with tasks, vars, templates, etc.

```bash
roles/
└── nginx/
    ├── tasks/
    ├── handlers/
    ├── templates/
    └── vars/
```

---

### 6. Idempotency in Ansible
**Idempotency:** Running a playbook multiple times doesn’t change the system if it’s already in the desired state.  
Example:  
```yaml
- name: Ensure package is installed
  apt:
    name: nginx
    state: present
```

---

## 🧠 Intermediate & Advanced Concepts

### 7. Secrets Management
Use **Ansible Vault** for encrypting secrets:

```bash
ansible-vault encrypt secrets.yml
```

**Example playbook usage:**
```yaml
- hosts: all
  vars_files:
    - secrets.yml
  tasks:
    - name: Use secret
      debug:
        msg: "Password is {{ db_password }}"
```

---

### 8. Handlers
Handlers are triggered by `notify` and run at the end of a playbook.

```yaml
tasks:
  - name: Update config
    template:
      src: config.j2
      dest: /etc/app/config
    notify: restart app

handlers:
  - name: restart app
    service:
      name: app
      state: restarted
```

---

### 9. Conditionals & Loops
**Conditional:**
```yaml
- name: Install on Debian
  apt:
    name: nginx
  when: ansible_os_family == "Debian"
```

**Loop:**
```yaml
- name: Add users
  user:
    name: "{{ item }}"
    state: present
  loop:
    - alice
    - bob
```

---

### 10. Facts
Facts are system properties gathered by Ansible. Use `gather_facts: true`.

**Custom Facts:**  
Create `/etc/ansible/facts.d/custom.fact`:
```ini
[web]
environment=prod
```

---

### 11. Error Handling
**Ignore errors:**
```yaml
- name: Maybe fail
  command: /bin/false
  ignore_errors: yes
```

**Rescue block:**
```yaml
- block:
    - name: Risky task
      command: /bin/false
  rescue:
    - name: Cleanup
      debug:
        msg: "Task failed"
```

---

### 12. delegate_to, local_action, run_once
- **delegate_to:** Run task on another host.
- **local_action:** Run task on control node.
- **run_once:** Run task only once.

---

## 🌍 Real-world Scenarios

### 13. Run Task Only If Previous Fails
Use `rescue` block or `failed_when`.

---

### 14. Multi-environment Setup
Use inventory groups and `group_vars`:

**inventory/prod:**
```ini
[web]
prod-web1

[db]
prod-db1
```

**group_vars/prod/vars.yml:**
```yaml
env: production
```

---

### 15. Zero-Downtime Deployment
Use `serial` and handlers:

```yaml
- hosts: web
  serial: 2
  tasks:
    - name: Deploy app
      copy:
        src: app.war
        dest: /opt/tomcat/
      notify: restart tomcat
```

---

### 16. Package Version Drift
Use version pinning:

```yaml
- name: Install exact version
  yum:
    name: nginx-1.18.0
    state: present
```

---

### 17. Optimize for 200+ Servers
- Increase `forks` in `ansible.cfg`
- Use `pipelining = true`
- Use `async` tasks

---

## 🔗 Integration & DevOps

### 18. CI/CD Integration
Example Jenkins pipeline:

```groovy
pipeline {
  agent any
  stages {
    stage('Deploy') {
      steps {
        sh 'ansible-playbook site.yml'
      }
    }
  }
}
```

---

### 19. Cloud Provisioning
Use `amazon.aws.ec2_instance` module:

```yaml
- name: Launch EC2 instance
  ec2_instance:
    key_name: mykey
    instance_type: t2.micro
    image_id: ami-123456
```

---

### 20. Docker & Kubernetes
**Docker:**
```yaml
- name: Run container
  docker_container:
    name: web
    image: nginx
```

**Kubernetes:**
```yaml
- name: Create deployment
  k8s:
    definition: "{{ lookup('file', 'deploy.yml') }}"
```

---

## 🛠️ Troubleshooting & Best Practices

### 21. Resume Failed Playbook
Use `--start-at-task`:

```bash
ansible-playbook site.yml --start-at-task="install app"
```

---

### 22. Debug Playbooks
Use verbose mode:
```bash
ansible-playbook site.yml -vvv
```

Step-by-step:
```bash
ansible-playbook site.yml --step
```

---

### 23. Best Practices
- Use roles
- Tag tasks
- Use `ansible-lint`
- Test with `molecule`

---

### 24. Testing
Use `molecule` for role testing:

```bash
molecule test
```

---

### 25. Pitfalls
- **Slow SSH:** Use SSH multiplexing
- **Large inventories:** Use dynamic inventory

---

## 🧪 Scenario-Based Questions

### 26. Multi-environment Setup
**Use group_vars and inventories:**
```yaml
# group_vars/dev/vars.yml
db_host: dev-db.example.com

# group_vars/prod/vars.yml
db_host: prod-db.example.com
```

---

### 27. Zero-Downtime Deployment
```yaml
- hosts: web
  serial: "20%"
  tasks:
    - name: Deploy
      copy:
        src: app.war
        dest: /opt/tomcat/
```

---

### 28. Secrets Management
Use Ansible Vault or HashiCorp Vault.

---

### 29. Partial Failures
```yaml
- name: Install package
  yum:
    name: httpd
  ignore_errors: yes
```

---

### 30. Performance Optimization
```yaml
- name: Async task
  command: /bin/long-running-script
  async: 300
  poll: 0
```

---

### 31. Dynamic Inventory (AWS)
Use `aws_ec2` plugin:

```yaml
# inventory_aws.yml
plugin: aws_ec2
regions:
  - us-east-1
```

---

### 32. Compliance Checking
```yaml
- name: Check OpenSSL version
  command: openssl version
  register: openssl_version
  failed_when: "'1.1.1' not in openssl_version.stdout"
```

---

### 33. CI/CD Integration
Use tags to run on specific hosts:

```bash
ansible-playbook site.yml --tags "deploy"
```

---

### 34. Large File Distribution
Use `synchronize`:

```yaml
- name: Sync large file
  synchronize:
    src: large_file.iso
    dest: /data/
```

---

### 35. Rollback
```yaml
- name: Backup config
  copy:
    src: /etc/app/config
    dest: /etc/app/config.bak

- name: Restore on failure
  copy:
    src: /etc/app/config.bak
    dest: /etc/app/config
  when: restore_needed
```

---

### 36. Conditional Deployment
```yaml
- name: Install if not present
  apt:
    name: nginx
    state: present
  register: install_result
  changed_when: install_result.changed
```

---

### 37. Delegation
```yaml
- name: Gather logs
  fetch:
    src: /var/log/app.log
    dest: /tmp/
  delegate_to: "{{ item }}"
  loop: "{{ groups['all'] }}"
```

---

### 38. Retries
```yaml
- name: Retry task
  command: /bin/flaky-script
  retries: 3
  delay: 5
```

---

### 39. Blue-Green Deployment
Use two groups in inventory: blue and green.

---

### 40. Kubernetes Management
Use `k8s` module or `helm`.

---

### 41. OS-Specific Issues
Use `when` conditions:

```yaml
- name: Install on Ubuntu
  apt:
    name: nginx
  when: ansible_distribution == "Ubuntu"

- name: Install on RHEL
  yum:
    name: nginx
  when: ansible_distribution == "RedHat"
```

---

### 42. Parallel vs Sequential
Use `serial` for sequential, default is parallel.

---

### 43. Compliance Reports
Use callback plugins or output to JSON:

```bash
ansible-playbook site.yml --json > result.json
```

---

### 44. Random Host Selection
Use `random` filter:

```yaml
- hosts: "{{ groups['web'] | shuffle | list[0:10] }}"
  tasks:
    - name: Test on random hosts
      ping:
```

---

### 45. OS Migration
Use roles with OS-specific vars and tasks.

---

## ✅ Summary

- Ansible is **agentless**, **YAML-based**, and **idempotent**.
- Use **roles** for reusability.
- Use **Vault** for secrets.
- Optimize with **async**, **pipelining**, and **dynamic inventories**.
- Test with **molecule** and **ansible-lint**.

---

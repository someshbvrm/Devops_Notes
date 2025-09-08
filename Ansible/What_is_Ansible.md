
***

# 🤖 **What is Ansible?**

**Ansible** is a powerful **open-source automation platform** acquired by Red Hat. It is designed to simplify complex IT tasks, making systems and applications easier to deploy and maintain.

- 🚀 **Purpose**: It is used for **IT automation, configuration management, application deployment, intra-service orchestration, and provisioning**.
- 🏃 **Agentless**: No software or agents need to be installed on the client machines it manages. It uses **SSH (for Linux/Unix)** and **WinRM (for Windows)** for secure communication.
- 📄 **Human-Readable Automation**: Configurations are defined in simple **YAML files** (called Playbooks), making automation accessible and easy to understand.

### 👉 Example Use Cases:
- 📦 Installing and managing software packages (Apache, Nginx, MySQL, etc.)
- 👥 Managing users, groups, and file permissions
- 🚀 Deploying applications from development to production
- ☁️ Automating cloud provisioning (AWS, Azure, GCP, etc.)
- 🎻 Orchestrating multi-tier application environments (e.g., start databases before app servers)

---

# ⚙️ **What is Configuration Management (CM)?**

**Configuration Management (CM)** is the practice of **automating the setup, maintenance, and consistency of systems and software** across their entire lifecycle.

- 🎯 **Goal**: To ensure that every server and application is in a **known, desired, and consistent state**, eliminating configuration drift between environments (Dev, QA, Prod).
- 🔧 **Method**: Instead of manual setup, CM tools like **Ansible, Puppet, Chef, and SaltStack** use code to define and enforce system state.

### 👉 Key Benefits:
- **✅ Consistency**: Every system is configured identically, reducing "it works on my machine" problems.
- **📈 Scalability**: Manage hundreds or thousands of servers as easily as one.
- **📚 Version Control & IaC**: Configuration is stored as code ("Infrastructure as Code"), allowing for versioning, peer review, and rollbacks.
- **🔍 Auditing & Compliance**: Easily report on and verify system configurations against security policies (e.g., CIS benchmarks).

---

# 🤖 **Ansible as a Configuration Management Tool**

Ansible excels at configuration management because it is:

- **🗣️ Declarative**: You describe the *desired state* of the system (e.g., "this package must be installed," "this service must be running"). Ansible figures out how to get there.
- **🔄 Idempotent**: You can run the same Playbook multiple times without causing unintended changes. If the system is already in the desired state, Ansible does nothing.
- **🛠️ Powerful**: With thousands of built-in modules, it can manage everything from OS packages and services to network devices and cloud resources.

### ✅ **In short**:
- **Ansible** = An automation tool (agentless, YAML-based).
- **Configuration Management** = The practice of keeping IT systems consistent.
- **Ansible is one of the most popular and accessible tools used for configuration management.**

---

# 🧩 **Infrastructure as Code (IaC) vs. Configuration Management (CM)**

While closely related and often used together, IaC and CM focus on different stages and aspects of IT automation.

### **🔹 1. Infrastructure as Code (IaC)**
👉 IaC is about **provisioning and managing the underlying infrastructure** (servers, networks, storage, cloud resources) using machine-readable definition files.

- **Focus**: The initial creation and lifecycle of infrastructure components.
- **Goal**: Make infrastructure reproducible, version-controlled, and disposable.
- **Tools**: **Terraform, AWS CloudFormation, Pulumi, Azure Resource Manager (ARM)**.

**✅ Example (Terraform - HCL):**
```hcl
resource "aws_instance" "web" {
  ami           = "ami-0abcdef1234567890"
  instance_type = "t2.micro"
}
```
*This code defines and provisions an EC2 instance.*

---

### **🔹 2. Configuration Management (CM)**
👉 CM is about **configuring and managing the software and OS** on already-provisioned infrastructure.

- **Focus**: The state of the software running on the infrastructure.
- **Goal**: Ensure all environments are configured consistently and correctly.
- **Tools**: **Ansible, Puppet, Chef, SaltStack**.

**✅ Example (Ansible Playbook - YAML):**
```yaml
- name: Install and configure Apache
  hosts: webservers
  become: yes
  tasks:
    - name: Ensure Apache is installed
      apt:
        name: apache2
        state: present
```
*This playbook configures software on existing servers.*

---

### **🔹 3. Key Differences**

| **Feature**       | **Infrastructure as Code (IaC)**                                  | **Configuration Management (CM)**                               |
| ----------------- | ----------------------------------------------------------------- | --------------------------------------------------------------- |
| **Scope**         | Creates & manages infra (VMs, networks, cloud services)           | Manages software, OS config, services on existing infra         |
| **Stage**         | "Day 0 / Day 1" → Provisioning                                    | "Day 2+" → Maintaining and enforcing system state               |
| **Idempotency**   | Often re-creates infra from scratch if drift occurs (e.g., Terraform) | Enforces desired state without a full rebuild (e.g., Ansible)     |
| **Primary Tools** | Terraform, CloudFormation, Pulumi                                 | Ansible, Puppet, Chef, SaltStack                                |
| **Output**        | Infrastructure resources (e.g., a new server)                     | Configured servers and applications                             |

---

### **🔹 4. How They Work Together**

In modern DevOps practices, **IaC and CM are combined** for a complete automation story:

1.  **IaC** (e.g., Terraform) provisions the servers, databases, and networks.
2.  **CM** (e.g., Ansible) then takes over to install applications, configure services, and apply security policies.

**👉 Example Workflow:**
- **Terraform** provisions **3 EC2 instances and a load balancer** in AWS.
- **Ansible** then configures **Nginx on the instances, deploys the application code, and sets up firewall rules**.

**✅ Perfect Analogy:**
- **IaC = Build the house (foundation, walls, roof).**
- **CM = Furnish the house, install electricity, and paint the walls (software, configs).**

---

# 🎯 **Ansible Use Cases**

Ansible's flexibility allows it to be used across various IT domains.

### **🔹 1. IT Operations**
- **Server Provisioning**: Automate the setup of Linux/Windows servers across cloud (AWS, Azure, GCP) or on-prem (VMware).
- **Application Deployment**: Consistently deploy applications (Java, Python, Node.js, .NET).
- **Service Management**: Ensure critical services (Nginx, Apache, MySQL) are running with the correct configuration.
- **User & Access Management**: Automate user creation, group management, and SSH key distribution.
- **Networking Automation**: Configure network devices from vendors like Cisco, Juniper, and F5.

### **🔹 2. DevOps**
- **CI/CD Pipelines**: Integrate with Jenkins, GitHub Actions, or GitLab CI to automatically deploy code after successful tests.
- **Infrastructure as Code (IaC)**: Manage infrastructure state using Ansible's own cloud modules or integrate with Terraform.
- **Container Orchestration**: Manage Kubernetes clusters, deploy Helm charts, and manage Docker containers.
- **Multi-tier Orchestration**: Bring up an entire application stack (DB → App → Web → LB) in a defined order with a single playbook.
- **Secrets Management**: Integrate with HashiCorp Vault, AWS Secrets Manager, or use **Ansible Vault** to encrypt sensitive data.

### **🔹 3. Patching & Updates**
- **OS Patch Management**: Apply security and kernel updates across a fleet of Linux/Windows servers.
- **Automated Patch Scheduling**: Run patching playbooks during predefined maintenance windows.
- **Selective Patching**: Patch only specific CVEs or services (e.g., OpenSSL).
- **Rolling Updates**: Update servers in batches to avoid application downtime in production environments.
- **Patch Reporting**: Generate detailed reports showing patched and unpatched systems.

### **🔹 4. Compliance & Security**
- **Baseline Enforcement**: Enforce security baselines (CIS, NIST, HIPAA) across all servers.
- **Policy as Code**: Define compliance rules in playbooks (e.g., password policies, disabled root login).
- **Audit & Drift Detection**: Scan systems for deviations from the approved, hardened configuration.
- **Security Hardening**: Automate firewall rules (iptables/ufw), disable insecure protocols (TELNET, FTP), and apply STIGs.
- **Monitoring Setup**: Deploy and configure monitoring agents for Prometheus, the ELK stack, or Splunk.

**✅ Summary**:
- **IT Ops**: Provisioning, deployments, user/service management.
- **DevOps**: CI/CD, IaC, container automation, orchestration.
- **Patching**: OS/software updates, scheduling, rolling updates.
- **Compliance**: Security baselines, auditing, hardening, reporting.

---

# 🏗️ **Ansible Architecture**

Ansible operates on a **simple, agentless, and push-based architecture**. It requires no additional software on the managed nodes, using standard remote management protocols.

### **🔹 Main Components**

1.  **📟 Control Node**
    - The machine where **Ansible is installed**.
    - It is the "brain" that runs playbooks and commands.
    - Can be a personal laptop, a dedicated server, or a CI/CD runner.

2.  **🖥️ Managed Nodes**
    - The systems being automated (servers, network devices, cloud instances).
    - They require **no Ansible-specific software**, only a Python interpreter (on Linux) and a remote connection method.

3.  **📋 Inventory**
    - A file (static or dynamic) that defines the **hosts and groups** Ansible manages.
    - **Example (INI format)**:
      ```ini
      [webservers]
      web1.example.com ansible_user=ubuntu
      web2.example.com ansible_user=ubuntu

      [dbservers]
      db1.example.com ansible_user=admin ansible_ssh_private_key_file=~/.ssh/db_key
      ```

4.  **📜 Playbooks**
    - YAML files containing a list of **plays** and **tasks** that define the automation.
    - **Example (`nginx.yml`)**:
      ```yaml
      - name: Install and start Nginx
        hosts: webservers
        become: yes
        tasks:
          - name: Install Nginx package
            apt:
              name: nginx
              state: present

          - name: Ensure Nginx is running
            service:
              name: nginx
              state: started
              enabled: yes
      ```

5.  **🧩 Modules**
    - Reusable, standalone scripts that Ansible executes on your behalf. They are the "tools in the toolkit."
    - Examples: `apt`, `yum`, `service`, `user`, `copy`, `aws_ec2`, `cisco_ios_command`.
    - Run with the `ansible` or `ansible-playbook` command.

6.  **🔌 Plugins**
    - Pieces of code that extend Ansible's core functionality (e.g., logging, caching, connection types, inventory sources).

7.  **🌌 Ansible Galaxy**
    - A hub for finding, reusing, and sharing community-developed **Roles** (collections of pre-packaged automation).
    - Command: `ansible-galaxy install geerlingguy.nginx`

8.  **🎭 Roles**
    - A predefined directory structure for organizing playbooks into reusable components. Roles allow for easy sharing and scaling of automation.

9.  **🔔 Handlers**
    - Special tasks that only run when notified by another task. They are typically used to restart services after a configuration change.

### **🔹 Communication Process**
1.  The user writes a **Playbook** or runs an **ad-hoc command** on the Control Node.
2.  Ansible parses the **Inventory** to determine which hosts to target.
3.  The Control Node connects to Managed Nodes via **SSH (Linux)** or **WinRM (Windows)**.
4.  Ansible pushes **small modules** (usually Python scripts) to the remote node, executes them, and cleans up after itself.
5.  The results of the task are sent back to the Control Node.
6.  Ansible moves to the next task or host, ensuring **idempotency** throughout.

### **🔹 High-Level Architecture Diagram**
```
┌───────────────────────┐
│    Control Node       │
│  (Ansible Installed)  │
│   - Runs Playbooks    │
└─────────┬─────────────┘
          │ (SSH / WinRM)
          │
  ┌───────┴─────────────────────┐
  │       Managed Nodes         │
  │ (No Agent Required)         │
  ├─────────────────────────────┤
  │ ┌─────────┐  ┌───────────┐  │
  │ │ Linux   │  │  Windows  │  │
  │ │ Server  │  │  Server   │  │
  │ └─────────┘  └───────────┘  │
  └─────────────────────────────┘
```

**✅ Summary:**
- **Control Node** runs the automation.
- **Inventory** defines the targets.
- **Playbooks (YAML)** define the automation steps.
- **Modules** do the actual work.
- Communication is **agentless** via **SSH/WinRM**.
- Operations are **idempotent** and resource-efficient.

---

# ⚔️ **Ansible Advantages over Other CM Tools**

| **Feature**         | **Ansible**               | **Puppet**                 | **Chef**                  | **SaltStack**            |
| ------------------- | ------------------------- | -------------------------- | ------------------------- | ------------------------ |
| **Architecture**    | 🚀 **Agentless** (SSH/WinRM) | Master-Agent               | Master-Agent              | Master-Minion (Agent)    |
| **Language**        | 📄 **YAML** (Easy to learn)  | Custom DSL                 | Ruby DSL                  | YAML + Python            |
| **Setup Complexity**| ✅ **Very Simple**         | Medium-High                | High                      | Medium                   |
| **Idempotency**     | ✅ **Yes (Default)**       | Yes                        | Yes                       | Yes                      |
| **Orchestration**   | ✅ **Strong**              | Weak                       | Medium                    | Medium-Strong            |
| **Execution Model** | ⏩ **Push** (Real-time)    | Pull (Periodic)            | Pull (Periodic)           | Both (Flexible)          |
| **Community**       | 🌎 **Strong (Galaxy)**     | Medium (Forge)             | Medium (Supermarket)      | Good                     |
| **Best For**        | Cloud, DevOps, CI/CD      | Large Enterprise IT        | Dev-heavy teams           | Real-time automation, Networking |

**✅ In short**:
- **Ansible wins** in **simplicity, agentless design, ease of learning, and strong DevOps/cloud integration.**
- **Puppet & Chef** are strong in **traditional enterprise environments** with complex, structured policies.
- **SaltStack** is excellent for **real-time automation and network device management**.

---

# 📋 **Ansible Prerequisites**

### **1. Control Node Requirements**
- **Operating System**: **Linux** (RHEL, CentOS, Ubuntu, Debian, etc.) or **macOS**. *Windows is not supported as a control node.*
- **Python**: **Python 3.8 or later** is required (Ansible is written in Python). Python 2.7 support was removed in Ansible 2.12.
- **Installation**: Install via `pip`, OS package manager (`apt`, `yum`, `dnf`), or from source.
- **Connectivity**: The control node must have network access to managed nodes on **SSH (22)** or **WinRM (5985/5986)** ports.

### **2. Managed Node Requirements**
- **For Linux/Unix**:
  - **SSH** access.
  - **Python 2.7** *or* **Python 3.5+** installed. (Most modern distributions have this pre-installed).
  - `sudo` privileges for the user if tasks require root access.
- **For Windows**:
  - **WinRM** configured and accessible.
  - **PowerShell 3.0 or higher**.
  - A valid authentication method (e.g., username/password, certificate).

### **3. User & Connectivity Setup**
- **SSH Key-Based Authentication** is highly recommended for Linux nodes for security and convenience.
- The user connecting to the nodes must have the appropriate **sudo permissions** to perform the required tasks.

### **4. Inventory File**
- Define your hosts in an inventory file, typically located at `/etc/ansible/hosts` or specified with the `-i` option.
- **Example (`inventory.ini`)**:
  ```ini
  [web]
  host1.example.com
  host2.example.com

  [db]
  db-server.example.com ansible_user=admin ansible_ssh_private_key_file=~/.ssh/db_key

  [all:vars]
  ansible_python_interpreter=/usr/bin/python3
  ```

### **5. Recommended Practices**
- **Version Control**: Store your playbooks and inventories in **Git**.
- **Use Virtual Environments**: Isolate your Ansible installation using `venv` or `virtualenv`.
- **Ansible Galaxy**: Leverage community roles for common setups.
- **Tower/AWX**: For enterprise needs (GUI, RBAC, scheduling, logging), use **Ansible Automation Platform** (commercial) or the upstream open-source project **AWX**.

**✅ Quick Checklist:**
- [ ] Control Node: Linux/macOS with Python 3.8+ and Ansible installed.
- [ ] Managed Nodes: SSH (Linux) / WinRM (Windows) access + Python interpreter.
- [ ] SSH keys configured for password-less login (Linux).
- [ ] Sudo privileges for the remote user.
- [ ] Inventory file populated with target hosts.

---

# 📋 **What is an Ansible Inventory?**

The **Inventory** is a crucial component that defines the **list of managed nodes (hosts)** and organizes them into **groups**. This allows you to target automation at specific sets of systems.

- **Formats**: **INI** (traditional) or **YAML** (more structured).
- **Location**: Default is `/etc/ansible/hosts`, but a custom file can be specified with the `-i` option.
- **Groups**: Hosts can be grouped (e.g., `[webservers]`, `[dbservers]`) for easier management.

### **🔹 Types of Inventory**

#### **1. Static Inventory**
A manually created file listing all hosts and their connection variables. Ideal for **stable environments** where hosts don't change frequently.

**Example (INI format - `inventory.ini`):**
```ini
[webservers]
web1.example.com ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/web_key
web2.example.com ansible_user=ubuntu

[dbservers]
db-primary.example.com ansible_user=admin ansible_password=MySecurePass123
```

**Example (YAML format - `inventory.yml`):**
```yaml
all:
  children:
    webservers:
      hosts:
        web1.example.com:
          ansible_user: ubuntu
        web2.example.com:
          ansible_user: ubuntu
    dbservers:
      hosts:
        db-primary.example.com:
          ansible_user: admin
          ansible_password: MySecurePass123
```
*👉 **Use case**: Lab environments, on-prem servers with static IPs.*

---

#### **2. Dynamic Inventory**
An inventory that is **generated on-the-fly** by executing a script or using a plugin that queries an external source, like a cloud provider's API.

- **Why?**: Essential for **cloud environments** (AWS, Azure, GCP) where instances are constantly created, terminated, or scaled.
- **How it works**: Ansible uses a plugin/script to call the cloud API, fetch a list of all instances, filter them (e.g., by tags), and format the results into its own inventory.

**Example: AWS EC2 Dynamic Inventory (`aws_ec2.yml`)**
```yaml
plugin: aws_ec2
regions:
  - us-east-1
  - us-west-2
filters:
  tag:Environment: production
keyed_groups:
  - key: tags.Role
    prefix: tag
```
*Run with: `ansible-inventory -i aws_ec2.yml --graph` to see the dynamically generated structure.*

**Example: Azure RM Dynamic Inventory (`azure_rm.yml`)**
```yaml
plugin: azure_rm
include_vm_resource_groups:
  - my-production-rg
auth_source: auto
```

*👉 **Use case**: Auto-scaling groups in AWS/Azure, dynamic Kubernetes clusters.*

---

### **🔹 Key Differences: Static vs. Dynamic**

| **Feature**       | **Static Inventory**                  | **Dynamic Inventory**                     |
| ----------------- | ------------------------------------- | ----------------------------------------- |
| **Definition**    | Manually defined in a file            | Automatically generated from an API/script |
| **Best For**      | Small, fixed environments             | Cloud, large-scale, auto-scaling infra    |
| **Scalability**   | Low (requires manual edits)           | High (auto-discovers new hosts)           |
| **Management**    | Manual effort                         | Automated, zero effort after setup        |
| **Example**       | `hosts.ini` file with static IPs      | AWS EC2, Azure RM, GCP Compute plugins    |

**✅ Summary**:
- Use **Static Inventory** for predictable, static infrastructure.
- Use **Dynamic Inventory** for elastic, cloud-native environments.

---

# ⚡ **Ansible Ad-hoc Commands**

Ad-hoc commands are for **quick, one-time tasks** that you don't want to write a full playbook for. They are perfect for learning, testing, and quick fixes.

**Syntax**: `ansible <host-pattern> -m <module> -a "<arguments>" [options]`

- **`<host-pattern>`**: A group or host from your inventory (e.g., `all`, `webservers`, `db1`).
- **`-m <module>`**: The Ansible module to execute (e.g., `ping`, `command`, `yum`).
- **`-a "<arguments>"`**: The options to pass to the module.
- **`--become`** or **`-b`**: Use sudo/root privileges.

### **🔹 Common Ad-hoc Command Examples**

#### **🏓 Connectivity & Testing**
```bash
# Ping all hosts to test SSH connectivity and Python availability
ansible all -m ping

# Ping only the 'webservers' group
ansible webservers -m ping
```

#### **💻 Running System Commands**
```bash
# Check uptime on all servers
ansible all -m command -a "uptime"

# Check disk usage on database servers
ansible dbservers -m shell -a "df -h"
```
*Note: Use the `shell` module for commands requiring pipes (`|`), redirects (`>`), etc.*

#### **📦 Package Management**
```bash
# Install Apache on RedHat-based systems (using yum)
ansible webservers -m yum -a "name=httpd state=present" --become

# Install Nginx on Debian-based systems (using apt)
ansible webservers -m apt -a "name=nginx state=latest" --become

# Remove the 'vim' package
ansible all -m apt -a "name=vim state=absent" --become
```

#### **🔧 Service Management**
```bash
# Ensure the httpd service is started and enabled on boot
ansible webservers -m service -a "name=httpd state=started enabled=yes" --become

# Restart the Nginx service
ansible webservers -m service -a "name=nginx state=restarted" --become

# Stop the MySQL service
ansible dbservers -m service -a "name=mysql state=stopped" --become
```

#### **📁 File & Directory Management**
```bash
# Copy a local file to all managed nodes
ansible all -m copy -a "src=/tmp/local.txt dest=/tmp/remote.txt mode=0644" --become

# Create a new directory
ansible all -m file -a "path=/opt/myapp state=directory mode=0755" --become

# Delete a file or directory
ansible all -m file -a "path=/tmp/junk state=absent" --become

# Change file ownership
ansible all -m file -a "path=/var/www/html owner=www-data group=www-data" --become
```

#### **👥 User & Group Management**
```bash
# Create a new user
ansible all -m user -a "name=devops shell=/bin/bash groups=sudo" --become

# Remove a user and their home directory
ansible all -m user -a "name=olduser state=absent remove=yes" --become

# Create a new group
ansible all -m group -a "name=developers state=present" --become
```

#### **🖥️ Gathering System Information**
```bash
# Gather and display all facts (system information) for all hosts
ansible all -m setup

# Filter facts to only show memory information
ansible all -m setup -a "filter=*memory*"
```

#### **🔄 Rebooting Systems**
```bash
# Reboot all servers and wait for them to come back up (for 10 minutes)
ansible all -m reboot --become
```

### **🔹 Real-World Scenario**
**Task**: "Check which servers need a security update and reboot them in batches of 5."

```bash
# Check for updates (Ubuntu)
ansible all -m apt -a "update_cache=yes" --become

# Upgrade only security packages
ansible all -m apt -a "upgrade=dist" --become --check # Dry-run first!
ansible all -m apt -a "upgrade=dist" --become -f 5    # Run for real, 5 hosts at a time

# Reboot in batches of 5
ansible all -m reboot --become -f 5
```

**✅ Summary**:
Ad-hoc commands are your Swiss Army knife for quick tasks: **testing connectivity, package/service management, file operations, user management, and gathering facts.** They save immense time compared to manual SSH sessions.

---

# 📜 **Ansible Playbooks**

A **Playbook** is a YAML file that defines a set of **plays** and **tasks** to be executed on managed nodes. Playbooks are more powerful than ad-hoc commands as they are **reusable, shareable, and can handle complex automation with variables, loops, conditionals, and error handling.**

### **Sample Playbooks**

#### **Example 1: Install & Configure Apache Web Server**
This playbook is idempotent and works across different OS families (Debian/RedHat).

```yaml
---
- name: Install and configure Apache
  hosts: webservers
  become: yes  # Run all tasks with sudo

  tasks:
    # Task for Debian-based systems (Ubuntu)
    - name: Install Apache on Debian
      apt:
        name: apache2
        state: present
      when: ansible_os_family == "Debian"

    # Task for RedHat-based systems (CentOS, RHEL)
    - name: Install Apache on RedHat
      yum:
        name: httpd
        state: present
      when: ansible_os_family == "RedHat"

    # Start and enable the service. Uses a variable to handle the different service names.
    - name: Ensure Apache is running and enabled
      service:
        name: "{{ 'apache2' if ansible_os_family == 'Debian' else 'httpd' }}"
        state: started
        enabled: yes

    # Deploy a custom index.html file
    - name: Deploy custom index.html
      copy:
        src: files/index.html
        dest: /var/www/html/index.html
        owner: "www-data"
        group: "www-data"
        mode: '0644'
      notify:
        - Restart Apache  # This handler will only run if the copy task changes the file.

  # Handlers are tasks that only run when notified by another task.
  handlers:
    - name: Restart Apache
      service:
        name: "{{ 'apache2' if ansible_os_family == 'Debian' else 'httpd' }}"
        state: restarted
```

---

#### **Example 2: Create a User & Deploy an SSH Key**
```yaml
---
- name: Set up user account with SSH key
  hosts: all
  become: yes
  vars:
    target_user: devops
    target_group: sudo

  tasks:
    - name: Ensure group exists
      group:
        name: "{{ target_group }}"
        state: present

    - name: Create user and add to group
      user:
        name: "{{ target_user }}"
        shell: /bin/bash
        groups: "{{ target_group }}"
        append: yes
        state: present

    - name: Deploy authorized SSH key for the user
      ansible.posix.authorized_key:
        user: "{{ target_user }}"
        state: present
        key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
```

---

### **How to Execute a Playbook**

1.  **Run a playbook:**
    ```bash
    ansible-playbook apache.yml
    ```

2.  **Check playbook syntax (linting):**
    ```bash
    ansible-playbook apache.yml --syntax-check
    ```

3.  **Perform a dry run (simulation):**
    ```bash
    ansible-playbook apache.yml --check
    ```
    *This shows what *would* change without making actual changes.*

4.  **Limit execution to a specific host or group:**
    ```bash
    ansible-playbook apache.yml --limit web1
    ```

5.  **Run with extra variables:**
    ```bash
    ansible-playbook user.yml -e "target_user=admin target_group=wheel"
    ```

6.  **Increase verbosity for debugging:**
    ```bash
    ansible-playbook apache.yml -v    # -v, -vv, -vvv for more detail
    ```

**✅ Summary**: Playbooks are the heart of Ansible automation, allowing you to define complex, repeatable, and documented processes for managing your infrastructure.
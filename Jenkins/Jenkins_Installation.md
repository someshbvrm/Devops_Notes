
---

# 🖥️ Installing Jenkins on Ubuntu

This guide walks through installing Jenkins on **Ubuntu** using `apt`, including **Java installation**, **repository setup**, and **starting Jenkins service**.

---

## 1️⃣ Update Ubuntu Packages

```bash
sudo apt update
```

---

## 2️⃣ Install Java

Jenkins requires Java. Install **OpenJDK 21 JRE** and `fontconfig`:

```bash
sudo apt install -y fontconfig openjdk-21-jre
```

Check Java version:

```bash
java -version
```

---

## 3️⃣ Add Jenkins Repository

Download the **Jenkins GPG key**:

```bash
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
```

Add Jenkins repository to `apt` sources:

```bash
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/" | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
```

Update the package list:

```bash
sudo apt update
```

---

## 4️⃣ Install Jenkins

```bash
sudo apt install -y jenkins
```

---

## 5️⃣ Start and Enable Jenkins Service

* Enable Jenkins to start on boot:

```bash
sudo systemctl enable jenkins
```

* Start Jenkins service:

```bash
sudo systemctl start jenkins
```

* Check status of Jenkins service:

```bash
sudo systemctl status jenkins
```

---

## 6️⃣ Access Jenkins Web UI

* Open a browser and go to:

```text
http://localhost:8080
```

* Wait for the **Unlock Jenkins** page.

* Retrieve the **initial admin password** from the console:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

* Copy the password and paste it into the Unlock Jenkins page.

---

## ⚠️ If Jenkins Fails to Start (Port Conflict)

1. Edit Jenkins service configuration:

```bash
sudo systemctl edit jenkins
```

2. Add the following to change the Jenkins port (example: `8081`):

```ini
[Service]
Environment="JENKINS_PORT=8081"
```

3. Reload the systemd daemon and restart Jenkins:

```bash
sudo systemctl daemon-reexec
sudo systemctl restart jenkins
```

---

✅ Jenkins should now be running and accessible via:

```text
http://localhost:8080
```

or the new port if changed.

---

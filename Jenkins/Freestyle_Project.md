
---

# 🚀 Jenkins Build Tutorial: WAR, JAR, Gradle, Ant & Agent Setup

This guide explains how to configure Jenkins to build **Maven WAR/JAR artifacts**, run builds using **Gradle & Ant**, and set up **Jenkins Agents (nodes)** with passwordless authentication.

---

## 📌 Prerequisites

Before starting, ensure:

* ✅ Jenkins installed & running (`http://<jenkins-server>:8080`)
* ✅ Java installed (`java -version`)
* ✅ Git installed (`git --version`)
* ✅ Maven / Gradle / Ant installed on Jenkins (Manage Jenkins → Global Tool Configuration)

---

## 🏗️ Building a WAR File with Maven

### 🔹 Steps

1. **Login to Jenkins** → `http://<jenkins-server>:8080`

2. Go to **Manage Jenkins → Tools**

   * Add **Maven installation** (e.g., Maven 3.9.x)

3. **New Item → Freestyle Project**

4. Inside the project configuration:

   * **Source Code Management (SCM):**

     * Git Repo:

       ```text
       https://github.com/kliakos/sparkjava-war-example.git
       ```

       or

       ```text
       https://github.com/jenkinsci/hello-world-maven-builder.git
       ```

   * **Build Steps → Invoke top-level Maven targets**

     * Maven Version: *(select installed Maven)*
     * Goals:

       ```bash
       clean test package
       ```

   * **Post-Build Actions → Archive the artifacts**

     * Files:

       ```text
       **/*.war
       ```

5. ✅ **Apply & Save**

6. Click **Build Now**

7. Check build logs under **Console Output**

---

## 📦 Building a JAR File with Maven

1. **Git Repo URL:**

   ```text
   https://github.com/jenkins-docs/simple-java-maven-app.git
   ```

2. Same steps as WAR build, except:

   * **Post-Build Actions → Archive the artifacts**

     ```text
     **/*.jar
     ```

     or

     ```text
     target/*.jar
     ```

---

## ⚡ Building with Gradle

1. Go to **Manage Jenkins → Tools → Gradle Installation**

   * Add Gradle (e.g., 7.x / 8.x)

2. **New Item → Freestyle Project**

   * **SCM → Git Repo:**

     ```text
     https://github.com/andyjduncan/gradle-example.git
     ```

   * **Build Step → Invoke Gradle Script**

     * Tasks:

       ```bash
       clean build
       ```

   * **Post-Build Actions → Archive the artifacts**

     ```text
     build/libs/*.jar
     ```

---

## 🛠️ Building with Ant

1. Go to **Manage Jenkins → Tools → Ant Installation**
2. **New Item → Freestyle Project**

   * **SCM → Git Repo:**

     ```text
     https://github.com/jenkinsci/model-ant-project.git
     ```

   * **Build Step → Invoke Ant**

     * Targets:

       ```bash
       clean jar
       ```

   * **Post-Build Actions → Archive the artifacts**

     ```text
     dist/*.jar
     ```

---

## ⚙️ Configuring Jenkins Agents (Nodes)

Jenkins agents help distribute builds across multiple machines.

---

### 🔹 Method 1: Launch Agent by Connecting to Controller

1. Go to **Manage Jenkins → Nodes → New Node**

   * Name: `linux-agent`
   * Type: **Permanent Agent**
   * Executors: `1`
   * Remote root dir: `/home/jenkins`
   * Labels: `linux`
   * Launch method:

     ```text
     Launch agent by connecting it to the controller
     ```

2. Save & open node page → copy **`agent.jar`** download link & JNLP command

3. On the **Agent Machine**:

   ```bash
   # Install Java
   java -version  

   # Create directory
   mkdir -p /home/jenkins && cd /home/jenkins  

   # Download agent.jar
   wget http://<jenkins-server>:8080/jnlpJars/agent.jar  

   # Run connection command (from Jenkins node page)
   java -jar agent.jar -jnlpUrl http://<jenkins-server>:8080/computer/linux-agent/slave-agent.jnlp -secret <secret> -workDir "/home/jenkins"
   ```

---

### 🔹 Method 2: Launch Agent via SSH

1. **Manage Jenkins → Nodes → New Node**

   * Executors: `1`
   * Remote root dir: `/home/jenkins`
   * Labels: `linux`
   * Launch method:

     ```text
     Launch agent via SSH
     ```

2. On the **Agent Machine**:

   ```bash
   # Install Java
   java -version  

   # Create Jenkins home
   mkdir -p /home/jenkins  

   # Allow SSH access from Jenkins master
   sudo systemctl enable ssh
   sudo systemctl start ssh
   ```

---

## 🔑 Passwordless SSH Authentication

To allow Jenkins Master to connect to Agents via SSH without a password:

### On **Master**

```bash
ssh-keygen -t rsa -b 4096
cat ~/.ssh/id_rsa.pub
```

### On **Agent**

```bash
mkdir -p ~/.ssh
nano ~/.ssh/authorized_keys
# Paste the public key here
chmod 600 ~/.ssh/authorized_keys
```

Now test from Master:

```bash
ssh jenkins@<agent-ip>
```

✅ If login works without a password, Jenkins can connect.

---

# 🎯 Summary

* ⚡ Built **WAR/JAR files with Maven**
* ⚡ Built apps with **Gradle & Ant**
* 🖥️ Configured **Jenkins agents** (via Controller & SSH)
* 🔑 Enabled **passwordless authentication** for automation

---

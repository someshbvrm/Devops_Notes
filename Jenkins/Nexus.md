
---

# 🏗️ Installing Nexus Repository Manager on Ubuntu

---

## 1️⃣ Update System & Install Java

```bash
sudo apt update -y        # Update package list
sudo apt install openjdk-17-jdk -y   # Install Java 17 JDK
```

---

## 2️⃣ Create Nexus User

```bash
sudo adduser nexus         # Create a dedicated Nexus user
sudo su - nexus            # Switch to Nexus user
```

---

## 3️⃣ Download and Extract Nexus

```bash
wget https://download.sonatype.com/nexus/3/nexus-3.83.1-03-linux-x86_64.tar.gz   # Download Nexus OSS
tar -xvzf nexus-3.83.1-03-linux-x86_64.tar.gz  # Extract the archive
mv nexus-* nexus            # Rename folder for convenience
```

---

## 4️⃣ Start Nexus

```bash
cd nexus/bin
./nexus start
```

* Nexus UI is available at:

```text
http://<server-ip>:8081
```

---

# 📦 Creating a Maven Repository in Nexus

1. Open Nexus UI:

```
http://<server-ip>:8081
```

2. **Login → Settings → Repositories → Create Repository**

3. Configure repository:

* **Name:** `mymaven` (hosted)
* **Type:** Release
* **Status:** Active
* **Permissions:** Enable admin permissions

✅ Nexus is now ready to host Maven builds.

---

# 🛠️ Jenkins Pipeline to Build Maven Project & Publish to Nexus

```groovy
pipeline {
    agent any

    tools {
        maven "maven3911"   // Name of Maven tool configured in Jenkins
    }

    stages {

        stage('Clean Workspace & Checkout') {
            steps {
                cleanWs()
                git 'https://github.com/someshbvrm/simple-maven-project-with-tests.git'
            }
        }

        stage('Build & Package') {
            steps {
                sh 'mvn -Dmaven.test.failure.ignore=true clean package'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQubeServer') {
                    withCredentials([string(credentialsId: 'sonarqubecredentials', variable: 'SONAR_TOKEN')]) {
                        sh 'mvn sonar:sonar -Dsonar.token=$SONAR_TOKEN -Dsonar.ws.timeout=60'
                    }
                }
            }
        }

        stage('Publish to Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'nexuscredentials', usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
                    sh '''
                        mvn deploy -DskipTests \
                        -Dnexus.username=$NEXUS_USER \
                        -Dnexus.password=$NEXUS_PASS
                    '''
                }
            }
        }
    }
}
```

---

## ✅ Key Notes

* Ensure **Maven tool** is configured in Jenkins (`Manage Jenkins → Global Tool Configuration`).
* **SonarQube server** should be added in Jenkins for analysis.
* Nexus credentials need to be created in **Jenkins → Credentials**.
* Repository in Nexus must match the Maven `distributionManagement` settings in `pom.xml`.

---

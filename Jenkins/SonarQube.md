
---

# 📌 Integrating SonarQube with Jenkins for Static Code Analysis

---

## 🚀 Step 1: Installing SonarQube on Ubuntu

### 1️⃣ Update & Upgrade Ubuntu

```bash
sudo apt update
sudo apt upgrade -y
```

### 2️⃣ Install Java (JDK 17)

> ⚠️ JDK 21 is not supported yet for SonarQube

```bash
sudo apt install openjdk-17-jdk -y
```

### 3️⃣ Install wget & unzip

```bash
sudo apt install wget unzip -y
```

### 4️⃣ Create SonarQube User

```bash
sudo adduser sonarqube
sudo usermod -aG sudo sonarqube
su sonarqube   # Switch to SonarQube user
cd ~
pwd   # Should be /home/sonarqube/
```

### 5️⃣ Download and Configure SonarQube

```bash
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-25.8.0.112029.zip

unzip sonarqube-25.8.0.112029.zip
mv sonarqube-25.8.0.112029 sonarqube  # Rename for convenience

# Set permissions
chown -R sonarqube:sonarqube /home/sonarqube/sonarqube
chmod -R 775 /home/sonarqube/sonarqube

# Start SonarQube
cd /home/sonarqube/sonarqube/bin/linux-x86-64
./sonar.sh start   # or sudo -u sonarqube ./sonar.sh start
```

### 6️⃣ Verify SonarQube

```bash
curl localhost:9000       # Terminal check
```

> 🌐 Access in browser: `http://<YOUR_EC2_PUBLIC_IP>:9000`
> Default credentials:
>
> * Username: `admin`
> * Password: `admin`

> 🔑 Generate a token from **My Account → Security → Generate Token**
> Example: `jenkins-project-token` (used in Jenkins for authentication)

---

## 🛠 Step 2: Configure Jenkins for SonarQube

### 1️⃣ Install "SonarQube Scanner" in Jenkins

* Go to **Manage Jenkins → Global Tool Configuration → SonarQube Scanner**
* Add the installed scanner.

### 2️⃣ Add Jenkins Credentials

* **Go to:** Credentials → System → Global Credentials → Add Credentials
* **Kind:** Secret text
* **Secret:** Paste token generated from SonarQube
* **ID:** `sonarqubecredentials`

### 3️⃣ Configure SonarQube Server

* **Go to:** Manage Jenkins → Configure System → SonarQube servers

* **Add SonarQube Server:**

  * Name: `SonarQubeServer`
  * Server URL: `http://<YOUR_EC2_PUBLIC_IP>:9000`
  * Server Authentication Token: `sonarqubecredentials`

* Save and Apply ✅

---

## 📝 Step 3: Jenkins Pipeline with SonarQube Integration

```groovy
pipeline {
    agent any

    tools {
        // Maven configured in Jenkins (e.g., "maven3911")
        maven "maven3911"
    }

    stages {
        stage('Build') {
            steps {
                cleanWs()  // Clean workspace before build
                // Clone project from Git
                git 'https://github.com/jglick/simple-maven-project-with-tests.git'

                // Build with Maven (ignore test failures)
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // Use SonarQube environment configured in Jenkins
                withSonarQubeEnv('SonarQubeServer') {
                    withCredentials([string(credentialsId: 'sonarqubecredentials', variable: 'SONAR_TOKEN')]) {
                        sh 'mvn sonar:sonar -Dsonar.login=$SONAR_TOKEN -Dsonar.ws.timeout=60'
                    }
                }
            }
        }
    }

    post {
        success {
            // Archive JAR artifacts
            archiveArtifacts 'target/*.jar'
        }
    }
}
```

> 🔹 Notes:
>
> * `SonarQubeServer` → Name of server in Jenkins **System → SonarQube**
> * `sonarqubecredentials` → Jenkins Secret Text Credential ID

---

### ✅ Summary

* Installed SonarQube on Ubuntu, started on port `9000`
* Generated authentication token for Jenkins
* Configured Jenkins tools and credentials
* Created a declarative pipeline integrating **Git + Maven + SonarQube**

---


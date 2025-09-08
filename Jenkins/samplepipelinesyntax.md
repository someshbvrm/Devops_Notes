
---

# 🟢 Jenkins Pipelines Cheat Sheet

---

## 1️⃣ **Hello World Pipeline**

A simple declarative pipeline example:

```groovy
pipeline {
    agent any

    stages {
        stage('Hello') {
            steps {
                echo '👋 Hello World'
            }
        }
    }
}
```

> ✅ Prints "Hello World" in the Jenkins console output.

---

## 2️⃣ **Git + Maven Declarative Pipeline**

Checkout code from Git and build using Maven:

```groovy
pipeline {
    agent any

    tools {
        maven "M3" // Maven version configured in Jenkins
    }

    stages {
        stage('Build') {
            steps {
                // Clone GitHub repository
                git 'https://github.com/jglick/simple-maven-project-with-tests.git'

                // Build project using Maven
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
                
                // For Windows agent:
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }

            post {
                success {
                    // Record test results and archive JAR
                    junit '**/target/surefire-reports/TEST-*.xml'
                    archiveArtifacts 'target/*.jar'
                }
            }
        }
    }
}
```

> 🔹 Uses **post block** to handle test results and artifacts only on successful builds.

---

## 3️⃣ **Scripted Pipeline Example**

A scripted pipeline equivalent with conditional logic for Unix/Windows agents:

```groovy
node {
    def mvnHome

    stage('Preparation') {
        // Clone repository
        git 'https://github.com/jglick/simple-maven-project-with-tests.git'

        // Get Maven tool from Jenkins configuration
        mvnHome = tool 'M3'
    }

    stage('Build') {
        // Run Maven build
        withEnv(["MVN_HOME=$mvnHome"]) {
            if (isUnix()) {
                sh '"$MVN_HOME/bin/mvn" -Dmaven.test.failure.ignore clean package'
            } else {
                bat(/"%MVN_HOME%\bin\mvn" -Dmaven.test.failure.ignore clean package/)
            }
        }
    }

    stage('Results') {
        // Record test results and archive artifacts
        junit '**/target/surefire-reports/TEST-*.xml'
        archiveArtifacts 'target/*.jar'
    }
}
```

> 🔹 **Scripted pipelines** provide more control and flexibility compared to declarative pipelines.

---

## 🔹 Key Notes

* **Maven Tool**: Ensure Maven version (e.g., `M3`) is configured in Jenkins → Manage Jenkins → Global Tool Configuration.
* **Git**: Jenkins agent must have Git installed.
* **JUnit**: Ensure tests generate `surefire-reports` for test reporting.
* **Artifacts**: JAR files from `target/` folder are archived for further use in pipelines.

---

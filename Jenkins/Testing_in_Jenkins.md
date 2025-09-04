In **CI/CD with Jenkins**, testing is done at multiple stages to ensure that code is correct, stable, and production-ready before it’s deployed.

We can split it into **CI testing** (early, fast feedback) and **CD testing** (deployment verification).

---

## **1. Testing in CI (Continuous Integration)**

These tests run automatically when developers push code to the repository.

| **Test Type**                      | **Purpose**                                                           | **Example Tools**                                 |
| ---------------------------------- | --------------------------------------------------------------------- | ------------------------------------------------- |
| **Unit Tests**                     | Test individual functions/classes in isolation to verify correctness. | JUnit (Java), pytest (Python), Mocha (JavaScript) |
| **Integration Tests**              | Check if multiple modules or services work together correctly.        | Postman/Newman, pytest, REST Assured              |
| **Static Code Analysis**           | Scan code for syntax errors, code smells, and potential bugs.         | SonarQube, ESLint, Pylint                         |
| **Static Security Testing (SAST)** | Detect security vulnerabilities in source code.                       | SonarQube Security, Bandit (Python), Checkmarx    |
| **Build Verification Tests (BVT)** | Ensure that the build is installable and runs without basic failures. | Maven/Gradle test suites                          |
| **Code Style/Linting**             | Ensure code follows team standards.                                   | Prettier, ESLint, Black (Python)                  |

💡 **Goal** in CI: Fail fast → detect issues early so they never reach staging/production.

---

## **2. Testing in CD (Continuous Delivery / Deployment)**

These tests happen after the application is packaged and deployed to a **staging** or **production** environment.

| **Test Type**                   | **Purpose**                                                       | **Example Tools**                                |
| ------------------------------- | ----------------------------------------------------------------- | ------------------------------------------------ |
| **Smoke Tests**                 | Quick tests to verify that the application runs after deployment. | Selenium, Cypress                                |
| **End-to-End (E2E) Tests**      | Simulate real user workflows.                                     | Selenium, Playwright, Cypress                    |
| **Performance Tests**           | Measure speed, scalability, and stability under load.             | JMeter, Gatling, Locust                          |
| **Security Tests (DAST)**       | Test the running app for vulnerabilities.                         | OWASP ZAP, Burp Suite                            |
| **User Acceptance Tests (UAT)** | Final business validation before production release.              | Manual or automated with BDD tools like Cucumber |
| **Canary Testing**              | Release to a small % of users, monitor for issues, then roll out. | AWS CodeDeploy, Kubernetes rollout               |
| **Chaos Testing**               | Intentionally break things to test resilience.                    | Chaos Monkey, Gremlin                            |

💡 **Goal** in CD: Ensure the deployed app is **working, safe, and fast** in a real environment.

---

## **Jenkins Pipeline Example Flow**

```groovy
pipeline {
    stages {
        stage('Build') {
            steps { sh 'mvn clean package' }
        }
        stage('Unit Tests') {
            steps { sh 'mvn test' }
        }
        stage('Static Analysis') {
            steps { sh 'sonar-scanner' }
        }
        stage('Integration Tests') {
            steps { sh './run_integration_tests.sh' }
        }
        stage('Deploy to Staging') {
            steps { sh './deploy_staging.sh' }
        }
        stage('Smoke Tests') {
            steps { sh './run_smoke_tests.sh' }
        }
        stage('Performance Tests') {
            steps { sh 'jmeter -n -t perf_test.jmx' }
        }
    }
}
```

---




Here’s your **ready-reference table** for CI/CD testing in Jenkins — it maps **pipeline stages → type of testing → example tools/commands** so you can plug them straight into a Jenkins pipeline.

---

## **Jenkins CI/CD Testing Reference Table**

| **Jenkins Stage**         | **Testing Type**                            | **Purpose**                                               | **Example Tools**             | **Example Command**                                |
| ------------------------- | ------------------------------------------- | --------------------------------------------------------- | ----------------------------- | -------------------------------------------------- |
| **Build**                 | Build Verification Test (BVT)               | Ensure the build compiles and packages successfully.      | Maven, Gradle, npm            | `mvn clean package`                                |
| **Unit Tests**            | Unit Testing                                | Test individual methods/functions for correctness.        | JUnit, pytest, Mocha          | `mvn test` / `pytest`                              |
| **Static Analysis**       | Static Code Analysis (SAST)                 | Detect code smells, security flaws, and style violations. | SonarQube, ESLint, Bandit     | `sonar-scanner` / `eslint src/`                    |
| **Integration Tests**     | API/Service Integration Testing             | Verify multiple components or services work together.     | Postman/Newman, REST Assured  | `newman run tests.json`                            |
| **Security Scan**         | Static Security Testing                     | Check for vulnerabilities in dependencies or code.        | OWASP Dependency-Check, Trivy | `dependency-check.sh`                              |
| **Deploy to Staging**     | Deployment Verification                     | Ensure staging deployment succeeds without errors.        | Bash scripts, Ansible         | `./deploy_staging.sh`                              |
| **Smoke Tests**           | Smoke Testing                               | Quick check to ensure deployed app runs.                  | Selenium, Cypress             | `npx cypress run --spec smoke.cy.js`               |
| **E2E Tests**             | End-to-End Testing                          | Simulate real user journeys.                              | Cypress, Playwright, Selenium | `npx playwright test`                              |
| **Performance Tests**     | Load & Stress Testing                       | Measure app speed, stability, scalability.                | JMeter, Gatling, Locust       | `jmeter -n -t test.jmx`                            |
| **Security Tests**        | Dynamic Application Security Testing (DAST) | Check running app for vulnerabilities.                    | OWASP ZAP, Burp Suite         | `zap-cli quick-scan http://staging-app`            |
| **User Acceptance Tests** | Business Logic Validation                   | Confirm features meet requirements.                       | Cucumber (BDD)                | `mvn test -Dcucumber.options="classpath:features"` |
| **Canary Testing**        | Partial Production Deployment               | Roll out to small % of users and monitor.                 | AWS CodeDeploy, Kubernetes    | `kubectl rollout status deploy/app`                |
| **Chaos Testing**         | Resilience Testing                          | Break things intentionally to test recovery.              | Chaos Monkey, Gremlin         | `gremlin attack-container`                         |

---

### **Pipeline Flow with These Stages**

```groovy
pipeline {
    stages {
        stage('Build') {
            steps { sh 'mvn clean package' }
        }
        stage('Unit Tests') {
            steps { sh 'mvn test' }
        }
        stage('Static Analysis') {
            steps { sh 'sonar-scanner' }
        }
        stage('Integration Tests') {
            steps { sh 'newman run integration_tests.json' }
        }
        stage('Security Scan') {
            steps { sh './dependency-check.sh' }
        }
        stage('Deploy to Staging') {
            steps { sh './deploy_staging.sh' }
        }
        stage('Smoke Tests') {
            steps { sh 'npx cypress run --spec smoke.cy.js' }
        }
        stage('E2E Tests') {
            steps { sh 'npx playwright test' }
        }
        stage('Performance Tests') {
            steps { sh 'jmeter -n -t perf_test.jmx' }
        }
        stage('Security Tests') {
            steps { sh 'zap-cli quick-scan http://staging-app' }
        }
        stage('User Acceptance Tests') {
            steps { sh 'mvn test -Dcucumber.options="classpath:features"' }
        }
        stage('Canary Testing') {
            steps { sh 'kubectl rollout status deploy/app' }
        }
        stage('Chaos Testing') {
            steps { sh 'gremlin attack-container' }
        }
    }
}
```

---


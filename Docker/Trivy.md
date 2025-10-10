# Comprehensive Trivy Tutorial for DevOps

## Table of Contents
1. [Introduction to Trivy](#introduction)
2. [Installation](#installation)
3. [Basic Usage](#basic-usage)
4. [Scanning Targets](#scanning-targets)
5. [Advanced Features](#advanced-features)
6. [CI/CD Integration](#ci-cd-integration)
7. [Best Practices](#best-practices)
8. [Troubleshooting](#troubleshooting)

## 1. Introduction to Trivy <a name="introduction"></a>

**Trivy** is a comprehensive, easy-to-use open-source vulnerability scanner for containers and other artifacts. It's specifically designed for DevOps and integrates seamlessly into CI/CD pipelines.

### Key Features:
- Comprehensive vulnerability detection (OS packages, language-specific packages)
- Simple installation and usage
- Fast scanning speed
- Multiple target support (containers, filesystems, Git repositories, etc.)
- CI/CD friendly

## 2. Installation <a name="installation"></a>

### Linux/macOS
```bash
# Using curl
curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh | sh -s -- -b /usr/local/bin

# Using Homebrew (macOS)
brew install aquasecurity/trivy/trivy

# Using apt (Debian/Ubuntu)
sudo apt-get install wget apt-transport-https gnupg lsb-release
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy
```
or 

```bash
wget https://github.com/aquasecurity/trivy/releases/download/v0.18.3/trivy_0.18.3_Linux-64bit.deb
sudo dpkg -i trivy_0.18.3_Linux-64bit.deb
```

### Windows
```powershell
# Using Chocolatey
choco install trivy

# Using Scoop
scoop bucket add extras
scoop install trivy
```

### Docker
```bash
docker pull aquasec/trivy:latest
```

### Verify Installation
```bash
trivy --version
```

## 3. Basic Usage <a name="basic-usage"></a>

### Scan a Docker Image
```bash
# Basic scan
trivy image python:3.8-alpine

# Scan with specific severity levels
trivy image --severity HIGH,CRITICAL python:3.8-alpine

# Scan and output as JSON
trivy image -f json -o results.json python:3.8-alpine

# Scan and exit with code 1 if vulnerabilities found
trivy image --exit-code 1 python:3.8-alpine

# Ignore specific vulnerabilities
trivy image --ignore-unfixed python:3.8-alpine
```

### Scan a Filesystem
```bash
# Scan current directory
trivy fs .

# Scan specific directory
trivy fs /path/to/your/project

# Scan with specific config file
trivy fs --config trivy.yaml /path/to/project
```

### Scan a Git Repository
```bash
trivy repo https://github.com/example/repo.git
```

## 4. Scanning Targets <a name="scanning-targets"></a>

### Container Images
```bash
# Scan from Docker daemon
trivy image your-image:tag

# Scan from remote registry (no docker required)
trivy image registry.example.com/your-image:tag

# Scan with authentication
trivy image --username user --password pass registry.example.com/image:tag
```

### Filesystem Scanning
```bash
# Scan for OS vulnerabilities
trivy fs --security-checks vuln /path/to/dir

# Scan for misconfigurations (IaC)
trivy fs --security-checks config /path/to/terraform

# Scan for secrets
trivy fs --security-checks secret /path/to/codebase

# Combine multiple checks
trivy fs --security-checks vuln,config,secret /path/to/project
```

### Kubernetes Resources
```bash
# Scan Kubernetes YAML files
trivy k8s --report summary cluster

# Scan specific namespace
trivy k8s --namespace production cluster

# Scan with specific context
trivy k8s --context your-context cluster
```

## 5. Advanced Features <a name="advanced-features"></a>

### Configuration File
Create `trivy.yaml`:
```yaml
db:
  repository: aquasec/trivy-db
  skip-update: false

cache:
  dir: /tmp/trivy

scan:
  skip-dirs:
    - .git
    - node_modules
  skip-files:
    - package-lock.json

report:
  format: table
  output: stdout

security-checks:
  - vuln
  - config
  - secret

severity:
  - UNKNOWN
  - LOW
  - MEDIUM
  - HIGH
  - CRITICAL

ignore-unfixed: false
exit-code: 1
```

### Custom Policies
```bash
# Create custom policies directory
mkdir -p policies/

# Create custom policy file (rego format)
cat > policies/my-policy.rego << EOF
package main

deny[msg] {
    input.Kind == "Deployment"
    not input.spec.template.spec.securityContext.runAsNonRoot
    msg = "Containers must not run as root"
}
EOF

# Scan with custom policies
trivy k8s --policy ./policies/ cluster
```

### Integration with Docker Build
```dockerfile
FROM alpine:3.14 AS builder
# Your build steps

FROM alpine:3.14
COPY --from=builder /app /app

# Scan the final image
# trivy image your-final-image:tag
```

### Air-Gapped Environments
```bash
# Download database offline
trivy image --download-db-only

# Copy database to air-gapped system
scp ~/.cache/trivy/db/trivy.db user@air-gapped-server:/tmp/

# Scan using offline database
trivy image --skip-db-update --cache-dir /tmp/ your-image:tag
```

## 6. CI/CD Integration <a name="ci-cd-integration"></a>

### GitHub Actions
```yaml
name: Security Scan
on: [push, pull_request]

jobs:
  security-scan:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v3

    - name: Build Docker image
      run: docker build -t my-app:${{ github.sha }} .

    - name: Run Trivy vulnerability scanner
      uses: aquasecurity/trivy-action@master
      with:
        image-ref: 'my-app:${{ github.sha }}'
        format: 'sarif'
        output: 'trivy-results.sarif'
        severity: 'HIGH,CRITICAL'
        exit-code: '1'

    - name: Upload Trivy scan results to GitHub Security tab
      uses: github/codeql-action/upload-sarif@v2
      if: always()
      with:
        sarif_file: 'trivy-results.sarif'
```

### GitLab CI
```yaml
stages:
  - test
  - build
  - security

trivy_scan:
  stage: security
  image:
    name: aquasec/trivy:latest
    entrypoint: [""]
  variables:
    TRIVY_USERNAME: "$CI_REGISTRY_USER"
    TRIVY_PASSWORD: "$CI_REGISTRY_PASSWORD"
  script:
    - trivy image --exit-code 1 --severity HIGH,CRITICAL $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
  only:
    - master
    - main
```

### Jenkins Pipeline
```groovy
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'docker build -t my-app:${GIT_COMMIT} .'
            }
        }
        stage('Security Scan') {
            steps {
                sh '''
                trivy image \
                  --exit-code 1 \
                  --severity HIGH,CRITICAL \
                  --format template \
                  --template "@contrib/gitlab.tpl" \
                  my-app:${GIT_COMMIT} > gl-dependency-scanning-report.json
                '''
            }
        }
    }
    post {
        always {
            archiveArtifacts artifacts: '**/*report.json', allowEmptyArchive: true
        }
    }
}
```

### Azure DevOps
```yaml
- task: Bash@3
  displayName: 'Trivy Security Scan'
  inputs:
    targetType: 'inline'
    script: |
      docker run \
        -v /var/run/docker.sock:/var/run/docker.sock \
        -v $(System.DefaultWorkingDirectory):/src \
        aquasec/trivy:latest \
        image --exit-code 1 --severity HIGH,CRITICAL your-image:$(Build.BuildId)
```

## 7. Best Practices <a name="best-practices"></a>

### 1. Regular Scanning
```bash
# Set up cron job for daily scans
0 2 * * * /usr/local/bin/trivy image --exit-code 0 --cache-dir /tmp/trivy your-image:latest >> /var/log/trivy-scan.log
```

### 2. Baseline Management
```bash
# Create baseline scan
trivy image --ignorefile .trivyignore your-image:baseline

# Use ignore file
cat > .trivyignore << EOF
# Ignore specific vulnerabilities
CVE-2021-44228
CVE-2022-0000 until=2023-12-31

# Ignore by package
alpine-baselayout@3.2.0-r16
EOF
```

### 3. Performance Optimization
```bash
# Use light database for faster scans
trivy image --light python:3.8-alpine

# Skip database update if recently updated
trivy image --skip-db-update your-image:tag

# Use server mode for multiple scans
trivy server --listen :4954 &
trivy client --remote http://localhost:4954 your-image:tag
```

### 4. Comprehensive Reporting
```bash
# Generate multiple report formats
trivy image \
  -f json -o trivy-report.json \
  -f template --template "@contrib/html.tpl" -o trivy-report.html \
  your-image:tag

# Send notifications
trivy image --format template --template "@contrib/slack.tpl" your-image:tag | \
  curl -X POST -H 'Content-type: application/json' --data @- $SLACK_WEBHOOK_URL
```

## 8. Troubleshooting <a name="troubleshooting"></a>

### Common Issues

**Database Update Failures**
```bash
# Clear cache and retry
rm -rf ~/.cache/trivy
trivy image --download-db-only

# Use specific mirror
trivy image --db-repository your-mirror/trivy-db your-image:tag
```

**Permission Issues**
```bash
# Run with appropriate permissions
sudo trivy image --reset
```

**Network Issues**
```bash
# Use proxy
https_proxy=http://proxy:port trivy image your-image:tag

# Offline mode
trivy image --offline-scan your-image:tag
```

### Debug Mode
```bash
# Enable debug logging
trivy image --debug your-image:tag

# Show all vulnerabilities including fixed ones
trivy image --no-ignore-unfixed your-image:tag
```

### Getting Help
```bash
# Check documentation
trivy --help
trivy image --help

# Check GitHub issues
# https://github.com/aquasecurity/trivy/issues

# Community support
# https://aquasecurity.github.io/trivy/latest/community/
```

## Conclusion

Trivy is an essential tool for modern DevOps workflows, providing comprehensive security scanning that integrates seamlessly into CI/CD pipelines. By following this tutorial, you can effectively implement vulnerability scanning, misconfiguration detection, and secret scanning across your infrastructure.

Remember to:
- Integrate Trivy early in your development process
- Set appropriate severity thresholds for your environment
- Regularly update the vulnerability database
- Use ignore files judiciously with proper justification
- Combine with other security tools for defense in depth

Happy scanning! 🔍🐳
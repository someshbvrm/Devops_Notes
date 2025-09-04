
---

## **Comprehensive CI/CD Toolchain Matrix**

| Stage                       | Tool                     | Free / Paid             | Cloud / Self-hosted                    | Best For                                            |
| --------------------------- | ------------------------ | ----------------------- | -------------------------------------- | --------------------------------------------------- |
| **Version Control (VCS)**   | GitHub                   | Free + Paid tiers       | Cloud                                  | Open-source projects, broad integration ecosystem   |
|                             | GitLab                   | Free + Paid tiers       | Cloud + Self-hosted                    | Teams wanting built-in CI/CD                        |
|                             | Bitbucket                | Free + Paid tiers       | Cloud + Self-hosted (Bitbucket Server) | Jira/Atlassian ecosystem users                      |
|                             | AWS CodeCommit           | Pay-per-usage           | Cloud (AWS)                            | AWS-centric teams needing private repos             |
|                             | Azure Repos              | Free + Paid tiers       | Cloud                                  | Microsoft/Azure ecosystem                           |
| **Integration (CI)**        | Jenkins                  | Free (Open-source)      | Self-hosted                            | Highly customizable pipelines, large plugin library |
|                             | GitHub Actions           | Free + Paid tiers       | Cloud                                  | GitHub-native automation                            |
|                             | GitLab CI/CD             | Free + Paid tiers       | Cloud + Self-hosted                    | All-in-one DevOps platform                          |
|                             | CircleCI                 | Free + Paid tiers       | Cloud + Self-hosted                    | Fast, scalable builds                               |
|                             | Travis CI                | Free + Paid tiers       | Cloud                                  | Open-source CI                                      |
|                             | AWS CodeBuild            | Pay-per-usage           | Cloud (AWS)                            | AWS-native CI                                       |
|                             | Azure Pipelines          | Free + Paid tiers       | Cloud                                  | Multi-platform CI/CD                                |
| **Build**                   | Maven                    | Free                    | Self-hosted                            | Java-based builds                                   |
|                             | Gradle                   | Free + Paid tiers       | Self-hosted                            | Java/Kotlin builds, performance tuning              |
|                             | npm                      | Free                    | Cloud + Local                          | Node.js projects                                    |
|                             | Webpack                  | Free                    | Local                                  | JavaScript bundling                                 |
|                             | Bazel                    | Free                    | Self-hosted                            | Large-scale builds (Google-style)                   |
|                             | Make                     | Free                    | Local                                  | C/C++ projects                                      |
| **Test**                    | JUnit / JUnit5           | Free                    | Local                                  | Java unit testing                                   |
|                             | Selenium                 | Free                    | Local + Cloud grid                     | UI/browser automation                               |
|                             | PyTest                   | Free                    | Local                                  | Python testing                                      |
|                             | Mocha                    | Free                    | Local                                  | JavaScript testing                                  |
|                             | Cypress                  | Free + Paid             | Local + Cloud dashboard                | E2E testing                                         |
|                             | Postman/Newman           | Free + Paid             | Local + Cloud                          | API testing                                         |
|                             | TestNG                   | Free                    | Local                                  | Java functional tests                               |
|                             | Cucumber                 | Free                    | Local                                  | BDD-style testing                                   |
| **Artifact Management**     | JFrog Artifactory        | Paid + Free OSS edition | Cloud + Self-hosted                    | Enterprise binary management                        |
|                             | Sonatype Nexus           | Free + Paid             | Cloud + Self-hosted                    | OSS-friendly artifact store                         |
|                             | AWS CodeArtifact         | Pay-per-usage           | Cloud (AWS)                            | AWS package registry                                |
|                             | Azure Artifacts          | Free + Paid             | Cloud                                  | Azure DevOps package hosting                        |
|                             | GitHub Packages          | Free + Paid             | Cloud                                  | GitHub-native artifact storage                      |
| **Deployment (CD)**         | Jenkins                  | Free                    | Self-hosted                            | Custom CD pipelines                                 |
|                             | Spinnaker                | Free                    | Self-hosted                            | Multi-cloud deployments                             |
|                             | Argo CD                  | Free                    | Self-hosted                            | GitOps Kubernetes CD                                |
|                             | FluxCD                   | Free                    | Self-hosted                            | Lightweight GitOps for Kubernetes                   |
|                             | Octopus Deploy           | Paid                    | Cloud + Self-hosted                    | Enterprise deployment orchestration                 |
|                             | AWS CodeDeploy           | Pay-per-usage           | Cloud (AWS)                            | AWS-native deployment                               |
|                             | Azure Pipelines          | Free + Paid             | Cloud                                  | Multi-cloud deployments                             |
| **Container Registry**      | Docker Hub               | Free + Paid             | Cloud                                  | General-purpose Docker hosting                      |
|                             | Amazon ECR               | Pay-per-usage           | Cloud (AWS)                            | AWS-native container registry                       |
|                             | Google Artifact Registry | Pay-per-usage           | Cloud (GCP)                            | GCP container registry                              |
|                             | Azure Container Registry | Pay-per-usage           | Cloud (Azure)                          | Azure-native registry                               |
|                             | Harbor                   | Free                    | Self-hosted                            | Enterprise-grade open-source registry               |
| **Config Management / IaC** | Ansible                  | Free                    | Self-hosted                            | Agentless automation                                |
|                             | Chef                     | Paid                    | Self-hosted + Cloud                    | Large infra automation                              |
|                             | Puppet                   | Paid                    | Self-hosted + Cloud                    | Enterprise config management                        |
|                             | SaltStack                | Free + Paid             | Self-hosted                            | High-speed orchestration                            |
|                             | Terraform                | Free + Paid (Cloud)     | Self-hosted + Cloud                    | IaC across clouds                                   |
| **Monitoring & Logging**    | Prometheus + Grafana     | Free                    | Self-hosted                            | Metrics + dashboards                                |
|                             | ELK Stack                | Free + Paid             | Self-hosted + Cloud                    | Log aggregation & search                            |
|                             | Datadog                  | Paid                    | Cloud                                  | Full-stack monitoring                               |
|                             | New Relic                | Free + Paid             | Cloud                                  | App + infra monitoring                              |
|                             | Splunk                   | Paid                    | Cloud + Self-hosted                    | Enterprise logging & analytics                      |
|                             | AWS CloudWatch           | Pay-per-usage           | Cloud (AWS)                            | AWS-native monitoring                               |
|                             | Azure Monitor            | Pay-per-usage           | Cloud (Azure)                          | Azure-native monitoring                             |
| **Security (DevSecOps)**    | SonarQube                | Free + Paid             | Self-hosted + Cloud                    | Code quality & security                             |
|                             | Snyk                     | Free + Paid             | Cloud                                  | Dependency & container scanning                     |
|                             | Aqua Security            | Paid                    | Cloud + Self-hosted                    | Container & Kubernetes security                     |
|                             | Checkmarx                | Paid                    | Cloud + Self-hosted                    | SAST security scanning                              |
|                             | Prisma Cloud (Twistlock) | Paid                    | Cloud + Self-hosted                    | Container & cloud security                          |
|                             | OWASP ZAP                | Free                    | Self-hosted                            | Pen-testing automation                              |

---



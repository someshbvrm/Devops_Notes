# Comprehensive Docker Guide

This guide covers **50+ Docker topics** from basics to advanced real-world practices, with commands, Dockerfile examples, docker-compose configurations, and use cases.

---

## 1. Introduction to Docker
Docker is a platform to build, ship, and run applications inside lightweight containers. It simplifies deployment by ensuring consistency across environments.

**Use case:** Package an app once, run it anywhere.

---

## 2. Docker Architecture
Docker uses a client-server model. The Docker CLI communicates with the Docker Daemon, which manages images, containers, networks, and volumes.

**Key components:** Docker Client, Docker Daemon, Docker Registry, Docker Objects.

---

## 3. Installing Docker
On Linux:
```bash
curl -fsSL https://get.docker.com | sh
sudo usermod -aG docker $USER
```

**Use case:** Set up Docker quickly on any server.

---

## 4. Docker Images
Images are read-only templates used to create containers. They are built in layers and stored locally or in registries.

```bash
docker pull nginx
docker images
```

**Use case:** Reuse base images for consistent environments.

---

## 5. Docker Containers
Containers are running instances of images. They provide isolation, portability, and lightweight execution.

```bash
docker run -d --name web nginx
docker ps
```

**Use case:** Deploy applications consistently.

---

## 6. Dockerfile Basics
A Dockerfile automates image creation. Example:
```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["python", "app.py"]
```

**Use case:** Define app environments reproducibly.

---

## 7. Multi-stage Builds
Optimize images by using multiple build stages.
```dockerfile
FROM golang:1.17 as builder
WORKDIR /app
COPY . .
RUN go build -o myapp

FROM alpine:3.15
COPY --from=builder /app/myapp /usr/local/bin/myapp
CMD ["myapp"]
```

**Use case:** Reduce image size.

---

## 8. Docker Volumes
Volumes persist container data.
```bash
docker volume create mydata
docker run -v mydata:/data nginx
```

**Use case:** Store databases or logs outside containers.

---

## 9. Docker Bind Mounts
Bind mounts map host paths to containers.
```bash
docker run -v $(pwd):/app nginx
```

**Use case:** Local development with live code updates.

---

## 10. Docker Networking Basics
Types: **bridge, host, none**.
```bash
docker network ls
docker run --network bridge nginx
```

**Use case:** Connect multiple containers.

---

## 11. Custom Networks
User-defined bridge networks allow service discovery.
```bash
docker network create mynet
docker run -d --name db --network mynet mysql
docker run -d --name app --network mynet myapp
```

**Use case:** Microservices communication.

---

## 12. Docker Compose
Compose manages multi-container apps.
```yaml
version: "3"
services:
  web:
    image: nginx
  db:
    image: mysql
```

```bash
docker-compose up -d
```

**Use case:** Simplify multi-service apps.

---

## 13. Docker Registry & Hub
Push/pull images to registries.
```bash
docker login
docker tag myapp user/myapp:v1
docker push user/myapp:v1
```

**Use case:** Share images.

---

## 14. Private Registries
Self-host registries for internal use.
```bash
docker run -d -p 5000:5000 registry:2
docker tag myapp localhost:5000/myapp
docker push localhost:5000/myapp
```

**Use case:** Enterprise security.

---

## 15. Managing Images
```bash
docker images
docker rmi <id>
docker system prune
```

**Use case:** Save disk space.

---

## 16. Container Resource Limits
Limit CPU and memory per container.
```bash
docker run -m 512m --cpus="1.0" nginx
```

**Use case:** Avoid noisy neighbors.

---

## 17. Container Logs
Check logs of running containers.
```bash
docker logs -f myapp
```

**Use case:** Debug issues.

---

## 18. Docker Inspect
Inspect containers and images.
```bash
docker inspect myapp
```

**Use case:** Retrieve configuration details.

---

## 19. Docker Events & Stats
Monitor containers in real time.
```bash
docker events
docker stats
```

**Use case:** Track activity and performance.

---

## 20. Debugging Containers
Use interactive shells.
```bash
docker exec -it myapp bash
```

**Use case:** Troubleshoot live apps.

---

## 21. Docker Swarm
Docker’s built-in orchestrator for clusters.
```bash
docker swarm init
docker service create --replicas 3 nginx
```

**Use case:** Manage multi-node clusters.

---

## 22. Swarm Stacks
Define apps in stack files.
```bash
docker stack deploy -c docker-compose.yml mystack
```

**Use case:** Complex deployments.

---

## 23. Swarm Overlay Networks
Connect services across nodes.
```bash
docker network create -d overlay mynet
```

**Use case:** Cross-node communication.

---

## 24. Docker Secrets
Securely store sensitive data.
```bash
echo "mypassword" | docker secret create db_pass -
```

**Use case:** Store credentials safely.

---

## 25. Docker Configs
Distribute configuration files in Swarm.
```bash
docker config create nginx_conf ./nginx.conf
```

**Use case:** Centralized configs.

---

## 26. Security Best Practices
- Use minimal base images (alpine).  
- Drop root privileges.  
- Scan images with tools like `trivy`.  

**Use case:** Hardening containers.

---

## 27. Storage Drivers
Docker supports AUFS, Overlay2, ZFS, Btrfs. Overlay2 is default.

**Use case:** Choose driver per workload.

---

## 28. Networking Drivers
- Bridge  
- Host  
- Overlay  
- Macvlan  

**Use case:** Advanced connectivity.

---

## 29. CI/CD with Docker
Example: GitHub Actions.
```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - run: docker build -t myapp .
      - run: docker push user/myapp:v1
```

**Use case:** Automated builds.

---

## 30. Docker with Jenkins
Pipeline integration example:
```groovy
pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh 'docker build -t myapp .'
      }
    }
  }
}
```

**Use case:** CI/CD automation.

---

## 31. Docker and Kubernetes
Docker was historically the main runtime; now replaced by containerd/CRI-O, but images still work in K8s.

**Use case:** Kubernetes workloads.

---

## 32. Docker Plugins
Plugins extend Docker (e.g., logging, networking, storage).

**Use case:** Extend functionality.

---

## 33. Docker System Commands
```bash
docker system df
docker system prune
```

**Use case:** Cleanup resources.

---

## 34. Docker Desktop
GUI for Windows/Mac users to manage containers easily.

**Use case:** Developer-friendly.

---

## 35. Docker Contexts
Switch between multiple environments.
```bash
docker context ls
docker context use myremote
```

**Use case:** Manage remote clusters.

---

## 36. Docker BuildKit
Improves build performance.
```bash
DOCKER_BUILDKIT=1 docker build .
```

**Use case:** Faster, secure builds.

---

## 37. Docker Healthchecks
Monitor container health.
```dockerfile
HEALTHCHECK CMD curl -f http://localhost/ || exit 1
```

**Use case:** Auto-restart unhealthy containers.

---

## 38. Immutable Infrastructure
Containers are stateless; replace instead of patching.

**Use case:** Reliable deployments.

---

## 39. Sidecar Containers
Run helpers (e.g., logging agents) alongside apps.

**Use case:** Observability.

---

## 40. Init Containers in Compose
Simulate init tasks in Docker Compose with dependencies.

**Use case:** Pre-setup tasks.

---

## 41. Docker Compose Override
Override configs in `docker-compose.override.yml`.

**Use case:** Different environments.

---

## 42. Docker Compose Profiles
Enable conditional services.
```yaml
profiles: ["debug"]
```

**Use case:** Environment flexibility.

---

## 43. Centralized Logging
Send container logs to ELK/EFK stack.

**Use case:** Monitor distributed apps.

---

## 44. Monitoring with Prometheus
Scrape Docker metrics via cAdvisor.

**Use case:** Performance tracking.

---

## 45. Container Security Tools
- Trivy  
- Clair  
- Anchore  

**Use case:** Vulnerability scanning.

---

## 46. Rootless Docker
Run Docker without root privileges.

**Use case:** Enhanced security.

---

## 47. Docker on Cloud Providers
AWS ECS, Azure ACI, Google Cloud Run support Docker images natively.

**Use case:** Serverless containers.

---

## 48. Best Practices for Dockerfiles
- Use multi-stage builds.  
- Minimize layers.  
- Pin versions.  
- Clean up after installs.  

**Use case:** Lean images.

---

## 49. Docker in Production
- Use monitoring/logging.  
- Implement security scanning.  
- Keep images small.  
- Automate deployments.  

**Use case:** Enterprise readiness.

---

## 50. Future of Docker
Docker continues as a developer tool, while Kubernetes dominates orchestration. Docker images remain universal.

**Use case:** Future-proof skills.

---

# ✅ Conclusion
This Docker guide covered **50+ topics** from basics to production-ready practices. Use it as a complete reference for developing, deploying, and managing containers.

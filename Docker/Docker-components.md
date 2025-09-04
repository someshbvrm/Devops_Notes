
---

### **Docker Architecture & Key Components**  
1. **Docker Daemon (`dockerd`)**  
   - Background service managing Docker objects (containers, images, networks).  
   - Communicates with the **Docker CLI** (command-line interface).  

2. **Docker Network**  
   - Manages container networking (bridge, host, overlay, macvlan).  
   - Commands:  
     ```bash
     docker network create my_net
     docker network ls
     ```

3. **Docker Volumes**  
   - Persistent storage for containers (bypasses UnionFS).  
   - Commands:  
     ```bash
     docker volume create my_vol
     docker volume inspect my_vol
     ```

4. **Dockerfile**  
   - Blueprint for building images (defines layers, dependencies, commands).  
   - Example:  
     ```dockerfile
     FROM alpine
     COPY . /app
     CMD ["/app/start.sh"]
     ```

5. **Docker Compose**  
   - Orchestrates **multi-container apps** using YAML (`docker-compose.yml`).  
   - Example:  
     ```yaml
     services:
       web:
         image: nginx
         ports: ["80:80"]
     ```

6. **Docker Machine** *(Legacy)*  
   - Provisions Docker hosts on VMs/cloud (largely replaced by Docker Desktop).  

7. **Docker Swarm**  
   - Native clustering for containers (alternative to Kubernetes).  
   - Commands:  
     ```bash
     docker swarm init
     docker service create --name web nginx
     ```

8. **Docker Services**  
   - Manages scalable container deployments in Swarm.  
   - Example:  
     ```bash
     docker service scale web=5
     ```

9. **Docker Stack**  
   - Deploys multi-service apps in Swarm using Compose files.  
   - Example:  
     ```bash
     docker stack deploy -c docker-compose.yml my_app
     ```

10. **Registries**  
    - **Docker Hub**: Public registry (default).  
    - **DTR (Docker Trusted Registry)**: Enterprise private registry (now **Mirantis Secure Registry**).  
    - **ECR (Elastic Container Registry)**: AWS-managed private registry.  

---

### **Key Notes**  
- **Docker Daemon + CLI**: Client-server model (CLI sends commands to `dockerd`).  
- **Volumes vs Bind Mounts**: Volumes are managed by Docker; bind mounts reference host paths.  
- **Swarm vs Kubernetes**: Swarm is simpler but less feature-rich than K8s.  
- **Compose vs Stack**: Compose is for development; Stack is for Swarm deployments.  


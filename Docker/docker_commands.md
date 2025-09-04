**Play with Docker (PWD)** is an online environment that allows you to run Docker commands in a browser-based terminal. Here’s a list of commonly used Docker commands you can apply in **Play with Docker**:

---

### **1. Docker Container Commands**
| Command | Description |
|---------|-------------|
| `docker run <image>` | Run a container from an image |
| `docker run -d <image>` | Run a container in detached mode |
| `docker run -it <image> /bin/bash` | Run an interactive container |
| `docker run -p <host_port>:<container_port> <image>` | Map a container port to host |
| `docker ps` | List running containers |
| `docker ps -a` | List all containers (including stopped ones) |
| `docker stop <container_id>` | Stop a running container |
| `docker start <container_id>` | Start a stopped container |
| `docker restart <container_id>` | Restart a container |
| `docker rm <container_id>` | Remove a stopped container |
| `docker rm -f <container_id>` | Force remove a running container |
| `docker logs <container_id>` | Show container logs |
| `docker exec -it <container_id> /bin/bash` | Execute a command in a running container |

---

### **2. Docker Image Commands**
| Command | Description |
|---------|-------------|
| `docker pull <image>` | Download an image from Docker Hub |
| `docker images` | List all downloaded images |
| `docker rmi <image_id>` | Remove an image |
| `docker build -t <tag> .` | Build an image from a Dockerfile |
| `docker push <username>/<image>` | Push an image to Docker Hub |

---

### **3. Docker Network Commands**
| Command | Description |
|---------|-------------|
| `docker network ls` | List all networks |
| `docker network create <network_name>` | Create a new network |
| `docker network inspect <network_name>` | Inspect a network |
| `docker network connect <network> <container>` | Connect a container to a network |
| `docker network disconnect <network> <container>` | Disconnect a container from a network |

---

### **4. Docker Volume Commands**
| Command | Description |
|---------|-------------|
| `docker volume ls` | List all volumes |
| `docker volume create <volume_name>` | Create a new volume |
| `docker volume inspect <volume_name>` | Inspect a volume |
| `docker volume rm <volume_name>` | Remove a volume |

---

### **5. Docker System & Info Commands**
| Command | Description |
|---------|-------------|
| `docker info` | Display Docker system information |
| `docker version` | Show Docker version |
| `docker stats` | Display live container resource usage |
| `docker system prune` | Remove unused containers, networks, and images |
| `docker top <container_id>` | Show running processes in a container |

---

### **6. Docker Compose (if supported)**
| Command | Description |
|---------|-------------|
| `docker-compose up` | Start services defined in `docker-compose.yml` |
| `docker-compose up -d` | Start services in detached mode |
| `docker-compose down` | Stop and remove services |
| `docker-compose ps` | List running services |
| `docker-compose logs` | View logs of services |

---

### **7. Docker Swarm (if supported)**
| Command | Description |
|---------|-------------|
| `docker swarm init` | Initialize a Swarm |
| `docker node ls` | List Swarm nodes |
| `docker service create --name <service> <image>` | Create a Swarm service |
| `docker service ls` | List services |
| `docker service scale <service>=<replicas>` | Scale a service |

---

### **Play with Docker (PWD) Specific Commands**
Since PWD is a temporary environment, some additional commands may be useful:
- `echo "Hello PWD"` → Test shell commands.
- `curl ifconfig.me` → Check the public IP.
- `ping google.com` → Test network connectivity.
- `docker login` → Log in to Docker Hub (if needed).

---

### **Example Workflow in PWD**
1. Pull an image:  
   ```bash
   docker pull nginx
   ```
2. Run a container:  
   ```bash
   docker run -d -p 80:80 --name my-nginx nginx
   ```
3. Check running containers:  
   ```bash
   docker ps
   ```
4. Access logs:  
   ```bash
   docker logs my-nginx
   ```

---

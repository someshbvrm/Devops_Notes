🐳 **Docker Deep Dive: Images, Containers, Lifecycle, Volumes & Networking**

---

## **1. Docker Image**
**📌 Definition:**  
A Docker image is a **read-only template** (blueprint) containing everything needed to run an application:  
- 📁 Application code  
- 📦 Libraries & dependencies  
- ⚙️ Environment variables  
- 🛠️ Configuration files  

**🔧 Nature:**  
- **Static & immutable** (unchanged once built)  

**🚀 Usage:**  
- Used to **create containers**  

**📋 Example:**  
- `nginx:latest` is an image pulled from Docker Hub.  
- You can use it to create **one or more containers**.

---

## **2. Docker Container**
**📌 Definition:**  
A Docker container is a **running instance** of a Docker image. It provides a lightweight, isolated environment for application execution.

**🔧 Nature:**  
- **Dynamic** (can be started, stopped, moved, or deleted)  

**🚀 Usage:**  
- Provides the **runtime environment** for your application.  

**📋 Example:**  
```bash
docker run nginx:latest  # Starts a container from the nginx image
```

---

## **🔁 Analogy**
- **Image** = Class (blueprint)  
- **Container** = Object (instance)  

---

## **📊 Key Differences**
| Feature              | Docker Image                     | Docker Container                  |
|----------------------|----------------------------------|-----------------------------------|
| **Type**             | Template / Blueprint             | Running instance                  |
| **State**            | Static, immutable                | Dynamic, mutable                  |
| **Purpose**          | To create containers             | To run applications               |
| **Storage**          | Stored in a registry             | Runs on Docker Engine             |
| **Lifecycle**        | Built once                       | Start, stop, restart, delete      |

---

# **🚀 Docker Lifecycle Commands**

## **1. Image Stage**
### **Pull an Image**
```bash
docker search nginx          # Search for images
docker pull nginx:latest     # Pull an image
```

### **Build an Image**
```bash
docker build -t myapp:1.0 .  # Build from Dockerfile
```

### **List Images**
```bash
docker images                # List all images
```

### **Remove an Image**
```bash
docker rmi myapp:1.0         # Remove an image
```

---

## **2. Container Stage**
### **Create & Run**
```bash
docker create --name mynginx nginx:latest    # Create (stopped)
docker run --name mynginx -d -p 8080:80 nginx:latest  # Create + start
```

### **Start / Stop / Restart**
```bash
docker start mynginx         # Start container
docker stop mynginx          # Stop container
docker restart mynginx       # Restart container
```

### **Pause / Resume**
```bash
docker pause mynginx         # Pause processes
docker unpause mynginx       # Resume processes
```

### **Inspect & Logs**
```bash
docker ps                    # List running containers
docker ps -a                 # List all containers
docker logs mynginx          # View logs
docker inspect mynginx       # Inspect details
```

### **Execute Commands**
```bash
docker exec -it mynginx bash   # Open shell in container
docker attach mynginx          # Attach to running container
```

### **Stop & Remove**
```bash
docker kill mynginx          # Force stop
docker rm mynginx            # Remove container
docker container prune       # Remove all stopped containers
```

---

## **🔄 Container Lifecycle Order**
1. `docker create` → Prepare container  
2. `docker start` → Run container  
3. `docker pause/unpause` → Optional  
4. `docker stop` or `docker restart`  
5. `docker kill` → Force stop  
6. `docker rm` → Remove container  

---

## **⚡ In Short**
- **Image** = Build & Pull  
- **Container** = Run → Manage → Stop → Remove  

---

# **🐳 Docker Commands Cheat Sheet**

## **🔹 1. Basic Info**
```bash
docker --version           # Check Docker version
docker info                # System info
docker run hello-world     # Test installation
```

## **🔹 2. Images**
```bash
docker images              # List images
docker pull ubuntu:20.04   # Pull image
docker build -t myapp:1.0 .  # Build image
docker rmi myapp:1.0       # Remove image
```

## **🔹 3. Containers**
```bash
docker run -it ubuntu:20.04 bash          # Interactive mode
docker run -d --name webserver -p 8080:80 nginx  # Detached mode
docker ps                 # List running containers
docker ps -a              # List all containers
```

## **🔹 4. Lifecycle**
```bash
docker start webserver     # Start container
docker stop webserver      # Stop container
docker restart webserver   # Restart container
docker kill webserver      # Kill container
docker rm webserver        # Remove container
```

## **🔹 5. Inspect & Logs**
```bash
docker logs webserver      # View logs
docker inspect webserver   # Inspect details
docker top webserver       # View processes
```

## **🔹 6. Execute Commands**
```bash
docker exec -it webserver bash     # Open shell
docker exec webserver ls /app      # Run command
```

## **🔹 7. Volumes**
```bash
docker volume create mydata        # Create volume
docker run -d -v mydata:/app/data ubuntu  # Use volume
docker volume ls                   # List volumes
```

## **🔹 8. Networks**
```bash
docker network ls                  # List networks
docker network create mynet        # Create network
docker run -d --name db --network mynet mysql  # Use network
```

## **🔹 9. Cleanup**
```bash
docker container prune      # Remove stopped containers
docker image prune -a       # Remove unused images
docker volume prune         # Remove unused volumes
```

---

# **🚀 Advanced Docker Commands**

## **🔹 1. System Cleanup**
```bash
docker system prune -a      # Remove all unused data
docker system df            # Check disk usage
```

## **🔹 2. Image Management**
```bash
docker tag myapp:1.0 myrepo/myapp:prod   # Tag image
docker push myrepo/myapp:prod            # Push to registry
docker save -o myapp.tar myapp:1.0       # Export image
docker load -i myapp.tar                 # Import image
```

## **🔹 3. Container Management**
```bash
docker commit mycontainer myapp:debug    # Create image from container
docker export mycontainer > mycontainer.tar  # Export container
cat mycontainer.tar | docker import - myapp:new  # Import as image
```

## **🔹 4. Resource Limits**
```bash
docker run -d --name limited --memory="512m" --cpus="1.5" nginx
```

## **🔹 5. Networking**
```bash
docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' mycontainer  # Get IP
docker network connect mynet mycontainer    # Connect to network
docker network disconnect mynet mycontainer # Disconnect
```

## **🔹 6. Logs & Monitoring**
```bash
docker logs -f myapp        # Follow logs
docker logs --tail 100 myapp # Last 100 lines
docker stats                # Live resource usage
```

## **🔹 7. Docker Compose**
```bash
docker-compose up -d        # Start services
docker-compose down         # Stop services
docker-compose up -d --build # Rebuild
docker-compose up -d --scale web=3  # Scale services
```

## **🔹 8. Security**
```bash
docker scan myapp:1.0       # Scan for vulnerabilities
docker run --privileged -it ubuntu bash  # Privileged mode
```

---

# **💾 Docker Volumes – Deep Dive**

## **📌 What is a Volume?**
- A **persistent storage** mechanism outside the container’s writable layer.  
- 📍 Default location: `/var/lib/docker/volumes/` (Linux)  

## **🚀 Why Use Volumes?**
- ✅ Data persistence (databases, logs, configs)  
- ✅ Share data between containers  
- ✅ Better performance than bind mounts  
- ✅ Easier backup & migration  

---

## **🔹 Types of Volumes**
### **1. Anonymous Volume**
```bash
docker run -d -v /data ubuntu  # Auto-named volume
```
- ❌ Temporary (deleted with container unless kept explicitly)

### **2. Named Volume**
```bash
docker volume create mydata
docker run -d -v mydata:/data ubuntu
```
- ✅ Persistent & reusable

### **3. Bind Mount**
```bash
docker run -d -v /host/path:/container/path ubuntu
```
- 🔄 Direct host-container mapping (ideal for development)

### **4. tmpfs Mount (Linux only)**
```bash
docker run -d --tmpfs /app/temp:rw,size=64m ubuntu
```
- 🚀 In-memory storage (volatile)

---

## **🔹 Volume Commands**
```bash
docker volume create mydata      # Create
docker volume ls                 # List
docker volume inspect mydata     # Inspect
docker volume rm mydata          # Remove
docker volume prune              # Remove unused
```

---

## **🔹 Real-World Examples**
### **📌 Example 1: MySQL Database**
```bash
docker run -d \
  --name mysql-db \
  -e MYSQL_ROOT_PASSWORD=root \
  -v mysql_data:/var/lib/mysql \
  mysql:8
```

### **📌 Example 2: Shared Data**
```bash
docker run -d --name app1 -v shared_data:/app/data ubuntu
docker run -d --name app2 -v shared_data:/app/data ubuntu
```

### **📌 Example 3: Development with Bind Mount**
```bash
docker run -d \
  --name webapp \
  -v $(pwd):/usr/src/app \
  -p 3000:3000 \
  node:18
```

### **📌 Example 4: tmpfs for Temp Data**
```bash
docker run -d --name fastapp --tmpfs /tmp:rw,size=128m ubuntu
```

---

## **🔹 Best Practices**
- Use **named volumes** for production persistence  
- Use **bind mounts** for development  
- Use **tmpfs** for sensitive/temporary data  
- Document volume usage in Dockerfiles/compose files  

---

# **🌐 Docker Networking – Deep Dive**

## **📌 What is Docker Networking?**
- Enables communication between:  
  1. 📡 Containers  
  2. 🖥️ Host machine  
  3. 🌍 External networks  

---

## **🔹 Default Networks**
| Network | Driver | Purpose |
|---------|--------|---------|
| `bridge` | Bridge | Default network for containers |
| `host`   | Host   | Shares host’s network stack |
| `none`   | Null   | No networking |

---

## **🔹 Network Drivers**
### **1. Bridge (Default)**
```bash
docker run -d --name web1 nginx
docker run -d --name web2 nginx
```
- Containers can communicate via DNS names

### **2. Host**
```bash
docker run -d --network host nginx
```
- No isolation; uses host’s network directly

### **3. None**
```bash
docker run -d --network none ubuntu
```
- 🚫 No network access

### **4. Overlay (Multi-Host)**
```bash
docker network create -d overlay my_overlay
```
- 🌐 For Docker Swarm / Kubernetes

### **5. Macvlan**
```bash
docker network create -d macvlan \
  --subnet=192.168.1.0/24 \
  --gateway=192.168.1.1 \
  -o parent=eth0 macvlan_net
```
- 📡 Assigns MAC address to container (appears as physical device)

---

## **🔹 Networking Commands**
```bash
docker network ls                 # List networks
docker network inspect bridge     # Inspect network
docker network create mynet       # Create network
docker run -d --network mynet nginx  # Use network
docker network connect mynet mycontainer  # Connect
docker network disconnect mynet mycontainer # Disconnect
docker network rm mynet           # Remove network
```

---

## **🔹 Real-World Examples**
### **📌 Example 1: Multi-Container App**
```bash
docker network create myapp_net
docker run -d --name db --network myapp_net mysql
docker run -d --name web --network myapp_net nginx
```

### **📌 Example 2: Host Network**
```bash
docker run -d --network host nginx
```

### **📌 Example 3: Isolated Container**
```bash
docker run -d --network none ubuntu
```

### **📌 Example 4: Macvlan**
```bash
docker run -d --network macvlan_net --name printer nginx
```

---

## **🔹 Best Practices**
- ✅ Use **custom bridge networks** for apps  
- ✅ Use **DNS names** instead of IPs  
- ✅ **Isolate services** with multiple networks  
- ✅ Avoid `--network host` unless necessary  
- ✅ Use **overlay networks** for multi-host setups  

---

# **🎯 Summary Cheat Sheet**
## **🐳 Images & Containers**
- **Image** = Template → `docker build/pull`  
- **Container** = Instance → `docker run/start/stop/rm`  

## **💾 Volumes**
- Use **named volumes** for persistence  
- Use **bind mounts** for development  
- Use **tmpfs** for temp data  

## **🌐 Networking**
- Use **custom networks** for isolation  
- Prefer **DNS discovery** over IPs  
- Use **overlay** for multi-host  

---

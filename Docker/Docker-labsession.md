
***

# 🐳 **Docker Command-Line Guide: From Basics to Operations**

## 🔍 **Inspecting & Managing the Docker Engine**

```bash
# Check if the Docker daemon is running
sudo systemctl status docker

# Start the Docker service (if not running)
sudo systemctl start docker

# Enable Docker to start on boot
sudo systemctl enable docker

# View system-wide information about the Docker installation
docker info

# Display the Docker version (client and server)
docker version
```

---

## 📦 **Managing Docker Images**

Images are the blueprints used to create containers.

```bash
# Download (pull) an image from a registry (Docker Hub by default)
docker pull nginx

# Pull a specific version (tag) of an image
docker pull nginx:1.24.0  # If no tag is specified, `:latest` is used

# List all images stored locally
docker images
# or
docker image ls

# Inspect the low-level details of an image (JSON format)
docker image inspect nginx
docker image inspect <image-id>  # Using the image ID

# Search Docker Hub for images
docker search nginx

# Remove a specific image
docker rmi nginx

# Force remove an image (even if it's in use by a container)
docker rmi -f nginx

# Remove all unused (dangling) images
docker image prune

# Remove all unused images, not just dangling ones (⚠️ use with caution!)
docker image prune -a
```

---

## 🐋 **Managing Docker Containers**

Containers are running instances of images.

### **Creating & Starting Containers**
```bash
# Create a container from an image but do not start it (random name assigned)
docker create ubuntu

# Create a container with a specific name
docker create --name server01 nginx

# Create a container from a specific image version
docker create --name server02 nginx:1.24.0

# The `run` command is a combination of `create` + `start`
# Run a container in the foreground (attached to your terminal)
docker run --name web01 nginx

# Run a container in the background (detached mode)
docker run -d --name web01 nginx

# Run a container, map a host port to a container port
docker run -d -p 8080:80 --name web01 nginx  # Host:Container
```

### **Listing & Inspecting Containers**
```bash
# List only running containers
docker ps

# List all containers (any status)
docker ps -a

# List only the IDs of all containers
docker ps -a -q

# Inspect all details of a container (JSON format)
docker inspect web01
```

### **Starting, Stopping, & Removing**
```bash
# Start a stopped container
docker start web01

# Stop a running container (graceful shutdown)
docker stop web01

# Force-stop a running container (SIGKILL)
docker kill web01

# Remove a stopped container
docker rm web01

# Force remove a container (even if running)
docker rm -f web01

# Stop and remove all containers in one command
docker rm -f $(docker ps -a -q)
```

---

## ⚙️ **Interactive Container Operations**

### **Keeping OS Containers Running**
Most base OS images (like `ubuntu`) are not designed to run a process in the foreground. They will start and exit immediately unless given a command to run.

```bash
# Run an OS container in detached, interactive mode with a TTY
# -d: detached, -i: interactive, -t: allocate a pseudo-TTY
docker run -dit --name my_ubuntu ubuntu

# This keeps the container running in the background.
```

### **Executing Commands in Running Containers**
**`docker attach` vs. `docker exec`**

- **`attach`**: Connects your terminal to the main running process (`PID 1`) inside the container. If that process is a shell and you exit it, the container will stop.
- **`exec`**: Runs a **new** command inside a running container. Exiting this command does not stop the container.

**✅ Always prefer `docker exec` for operational tasks.**

```bash
# ❌ NOT RECOMMENDED: Attach to the main process. Exiting will stop the container.
docker attach my_ubuntu
# Type 'exit' -> container stops.

# ✅ RECOMMENDED: Execute a new shell process inside the running container.
docker exec -it my_ubuntu /bin/bash
# You are now in a Bash shell inside the container.
# Type 'exit' -> only the shell session ends; the container stays running.
```

**Run commands without an interactive shell:**
```bash
# Execute a single command and get the output
docker exec my_ubuntu ls /         # List root directory
docker exec my_ubuntu ps -ef       # Show running processes
docker exec my_ubuntu cat /etc/os-release # Check OS info
```

---

## 📋 **Logging & Monitoring**

```bash
# View the logs from a container's main process (STDOUT/STDERR)
docker logs web01

# Follow the logs in real-time (like `tail -f`)
docker logs -f web01

# View logs with timestamps
docker logs -t web01

# View the last 10 lines of logs
docker logs --tail 10 web01

# Monitor Docker events in real-time (open in a separate terminal)
docker events
# Now, in another terminal, run `docker run hello-world` to see events stream.
```

---

## 🚀 **Pro Tips & Useful One-Liners**

```bash
# Stop all running containers
docker stop $(docker ps -q)

# Remove all stopped containers
docker rm $(docker ps -a -q)

# Remove all images (⚠️ NUCLEAR OPTION - use with extreme caution!)
docker rmi -f $(docker images -q)

# Get a shell inside a container that lacks `bash` (e.g., Alpine-based images)
docker exec -it <container-name> /bin/sh

# Copy a file from your host machine into a running container
docker cp /path/on/host/file.txt my_ubuntu:/path/in/container/

# Copy a file from a container to your host machine
docker cp my_ubuntu:/path/in/container/log.txt /path/on/host/

# Rename a container
docker rename old_name new_name

# View resource usage statistics (CPU, memory, network) for all containers
docker stats

# View resource usage for a specific container
docker stats web01
```

---

## ✅ **Summary & Best Practices**

- **🔐 Use `sudo` or add your user to the `docker` group** to run commands without root.
- **🏷️ Always name your containers** (`--name`) for easier management.
- **🐋 Use `docker exec` instead of `docker attach`** to avoid accidentally stopping containers.
- **🧹 Clean up regularly** using `prune` commands to remove unused images, containers, and networks.
- **📜 Use `docker logs`** to debug applications running inside containers.
- **⚡ Use `docker run -d`** to start containers in detached mode for long-running services.
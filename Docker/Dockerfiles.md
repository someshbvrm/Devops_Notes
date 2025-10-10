
---

# 🔹 **1. NGINX serving static website**

**Dockerfile**

```dockerfile
# Use official NGINX base image
FROM nginx:alpine

# Copy static website files into NGINX HTML directory
COPY ./html /usr/share/nginx/html

# Expose port 80
EXPOSE 80

# Start NGINX in foreground
CMD ["nginx", "-g", "daemon off;"]
```

**docker-compose.yml**

```yaml
version: "3.9"

services:
  nginx:
    build: .
    container_name: nginx-static
    ports:
      - "80:80"
```

---

# 🔹 **2. Tomcat with custom WAR (e.g., Jenkins)**

**Dockerfile**

```dockerfile
# Use official Tomcat base image
FROM tomcat:9.0-jdk17-temporal

# Deploy WAR file
COPY jenkins.war /usr/local/tomcat/webapps/

# Expose Tomcat default port
EXPOSE 8080
```

**docker-compose.yml**

```yaml
version: "3.9"

services:
  tomcat:
    build: .
    container_name: tomcat-jenkins
    ports:
      - "8080:8080"
```

---

# 🔹 **3. Spring Boot JAR (PetClinic style)**

**Dockerfile**

```dockerfile
# Start from lightweight OpenJDK image
FROM openjdk:17-jdk-slim

# Set working directory
WORKDIR /app

# Copy built JAR
COPY target/*.jar app.jar

# Expose Spring Boot port
EXPOSE 8080

# Run the JAR
ENTRYPOINT ["java", "-jar", "app.jar"]
```

**docker-compose.yml**

```yaml
version: "3.9"

services:
  springboot:
    build: .
    container_name: springboot-app
    ports:
      - "8080:8080"
```

---

# 🔹 **4. Node.js App**

**Dockerfile**

```dockerfile
# Use Node.js LTS version
FROM node:18-alpine

# Working directory inside container
WORKDIR /app

# Copy package.json and install deps first
COPY package*.json ./
RUN npm install --production

# Copy rest of the code
COPY . .

# Expose Node.js port
EXPOSE 3000

# Start app
CMD ["node", "src/index.js"]
```

**docker-compose.yml**

```yaml
version: "3.9"

services:
  nodeapp:
    build: .
    container_name: node-app
    ports:
      - "3000:3000"
    volumes:
      - .:/app   # useful for development
```

---

# 🔹 **5. Python Flask App**

**Dockerfile**

```dockerfile
# Use Python 3.11 slim
FROM python:3.11-slim

# Working directory
WORKDIR /app

# Install dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy source code
COPY . .

# Expose Flask port
EXPOSE 5000

# Run app
CMD ["python", "app.py"]
```

**docker-compose.yml**

```yaml
version: "3.9"

services:
  pythonapp:
    build: .
    container_name: python-app
    ports:
      - "5000:5000"
    volumes:
      - .:/app   # optional for development
```

---

# 🔹 **6. MySQL Database (extra example)**

**Dockerfile**

```dockerfile
# Use official MySQL image
FROM mysql:8.0

# Expose default MySQL port
EXPOSE 3306
```

**docker-compose.yml**

```yaml
version: "3.9"

services:
  mysql:
    build: .
    container_name: mysql-db
    environment:
      MYSQL_ROOT_PASSWORD: rootpass
      MYSQL_DATABASE: mydb
      MYSQL_USER: myuser
      MYSQL_PASSWORD: mypass
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql

volumes:
  mysql_data:
```

---

# 🔹 **7. Fullstack Example (React + API + DB)**

👉 This shows **multiple services in one compose file**.

**Dockerfile (React frontend)**

```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install --production

COPY . .

RUN npm run build

EXPOSE 3000
CMD ["npm", "start"]
```

**docker-compose.yml**

```yaml
version: "3.9"

services:
  frontend:
    build: ./frontend
    container_name: react-frontend
    ports:
      - "3000:3000"

  backend:
    build: ./backend
    container_name: node-backend
    ports:
      - "5000:5000"
    environment:
      - DB_HOST=mysql
      - DB_USER=myuser
      - DB_PASSWORD=mypass
      - DB_NAME=mydb

  mysql:
    image: mysql:8.0
    container_name: mysql-db
    environment:
      MYSQL_ROOT_PASSWORD: rootpass
      MYSQL_DATABASE: mydb
      MYSQL_USER: myuser
      MYSQL_PASSWORD: mypass
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql

volumes:
  mysql_data:
```

---

Great 👍 Let’s add an **NGINX Reverse Proxy** example where NGINX sits in front and routes requests to multiple backend services.

---

# 🔹 **NGINX Reverse Proxy Example**

We’ll set up:

* **NGINX** → Reverse proxy (listens on port 80)
* **Spring Boot app** → Runs on port 8080 internally
* **Python Flask app** → Runs on port 5000 internally

---

### **1. Dockerfile for NGINX Reverse Proxy**

```dockerfile
# Use official NGINX image
FROM nginx:alpine

# Copy custom reverse proxy configuration
COPY nginx.conf /etc/nginx/nginx.conf

# Expose port 80
EXPOSE 80

# Start NGINX
CMD ["nginx", "-g", "daemon off;"]
```

---

### **2. nginx.conf (reverse proxy rules)**

```nginx
events {}

http {
    server {
        listen 80;

        location /springboot/ {
            proxy_pass http://springboot:8080/;
        }

        location /python/ {
            proxy_pass http://pythonapp:5000/;
        }
    }
}
```

👉 Explanation:

* Requests to `http://localhost/springboot/` → sent to **Spring Boot** service.
* Requests to `http://localhost/python/` → sent to **Python Flask** service.

---

### **3. Spring Boot Dockerfile**

```dockerfile
FROM openjdk:17-jdk-slim
WORKDIR /app
COPY target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

---

### **4. Python Flask Dockerfile**

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 5000
CMD ["python", "app.py"]
```

---

### **5. docker-compose.yml**

```yaml
version: "3.9"

services:
  nginx:
    build: ./nginx
    container_name: reverse-proxy
    ports:
      - "80:80"
    depends_on:
      - springboot
      - pythonapp

  springboot:
    build: ./springboot
    container_name: springboot-app
    expose:
      - "8080"

  pythonapp:
    build: ./pythonapp
    container_name: python-app
    expose:
      - "5000"
```

---

✅ Now you can start everything with:

```bash
docker-compose up -d
```

And access:

* **Spring Boot** → `http://localhost/springboot/`
* **Python Flask** → `http://localhost/python/`

---

Awesome 🚀 Let’s build a **fullstack setup with NGINX reverse proxy**.
We’ll have:

* **NGINX** → Acts as reverse proxy (entry point, port `80`)
* **React frontend** → Runs on port `3000` internally
* **Node.js backend API** → Runs on port `5000` internally
* **MySQL database** → Runs on port `3306` internally

---

# 🔹 **1. NGINX Reverse Proxy**

**nginx/Dockerfile**

```dockerfile
FROM nginx:alpine

# Copy custom NGINX config
COPY nginx.conf /etc/nginx/nginx.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

**nginx/nginx.conf**

```nginx
events {}

http {
    server {
        listen 80;

        # React frontend
        location / {
            proxy_pass http://frontend:3000/;
        }

        # Node.js backend
        location /api/ {
            proxy_pass http://backend:5000/;
        }
    }
}
```

---

# 🔹 **2. React Frontend**

**frontend/Dockerfile**

```dockerfile
FROM node:18-alpine

WORKDIR /app

# Copy and install dependencies
COPY package*.json ./
RUN npm install --production

# Copy code and build React app
COPY . .
RUN npm run build

EXPOSE 3000
CMD ["npm", "start"]
```

---

# 🔹 **3. Node.js Backend API**

**backend/Dockerfile**

```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install --production

COPY . .

EXPOSE 5000
CMD ["node", "server.js"]
```

---

# 🔹 **4. MySQL Database**

No Dockerfile needed (use official image).

---

# 🔹 **5. docker-compose.yml**

```yaml
version: "3.9"

services:
  nginx:
    build: ./nginx
    container_name: reverse-proxy
    ports:
      - "80:80"
    depends_on:
      - frontend
      - backend

  frontend:
    build: ./frontend
    container_name: react-frontend
    expose:
      - "3000"

  backend:
    build: ./backend
    container_name: node-backend
    expose:
      - "5000"
    environment:
      - DB_HOST=mysql
      - DB_USER=myuser
      - DB_PASSWORD=mypass
      - DB_NAME=mydb
    depends_on:
      - mysql

  mysql:
    image: mysql:8.0
    container_name: mysql-db
    environment:
      MYSQL_ROOT_PASSWORD: rootpass
      MYSQL_DATABASE: mydb
      MYSQL_USER: myuser
      MYSQL_PASSWORD: mypass
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql

volumes:
  mysql_data:
```

---

# ✅ How it Works

* **Users hit `http://localhost/`** → NGINX sends traffic to **React frontend**
* **Users hit `http://localhost/api/`** → NGINX sends traffic to **Node.js backend**
* **Backend talks to MySQL** using environment variables

---

Perfect 👍 Let’s upgrade the setup by adding **SSL/TLS (HTTPS)** support to the NGINX reverse proxy.
We’ll use **self-signed certificates** for testing, but you can later switch to **Let’s Encrypt** in production.

---

# 🔹 **1. Generate SSL Certificates (local dev)**

Run these commands on your host machine **before building Docker**:

```bash
mkdir -p nginx/certs

openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout nginx/certs/nginx.key \
  -out nginx/certs/nginx.crt \
  -subj "/CN=localhost"
```

This creates:

* `nginx/certs/nginx.key` → private key
* `nginx/certs/nginx.crt` → certificate

---

# 🔹 **2. NGINX Reverse Proxy with SSL**

**nginx/Dockerfile**

```dockerfile
FROM nginx:alpine

# Copy SSL certs and config
COPY certs /etc/nginx/certs
COPY nginx.conf /etc/nginx/nginx.conf

EXPOSE 80 443

CMD ["nginx", "-g", "daemon off;"]
```

**nginx/nginx.conf**

```nginx
events {}

http {
    server {
        listen 80;
        server_name localhost;

        # Redirect all HTTP to HTTPS
        return 301 https://$host$request_uri;
    }

    server {
        listen 443 ssl;
        server_name localhost;

        ssl_certificate     /etc/nginx/certs/nginx.crt;
        ssl_certificate_key /etc/nginx/certs/nginx.key;

        # React frontend
        location / {
            proxy_pass http://frontend:3000/;
        }

        # Node.js backend
        location /api/ {
            proxy_pass http://backend:5000/;
        }
    }
}
```

---

# 🔹 **3. React Frontend**

(same as before)
**frontend/Dockerfile**

```dockerfile
FROM node:18-alpine

WORKDIR /app
COPY package*.json ./
RUN npm install --production
COPY . .
RUN npm run build

EXPOSE 3000
CMD ["npm", "start"]
```

---

# 🔹 **4. Node.js Backend API**

(same as before)
**backend/Dockerfile**

```dockerfile
FROM node:18-alpine

WORKDIR /app
COPY package*.json ./
RUN npm install --production
COPY . .

EXPOSE 5000
CMD ["node", "server.js"]
```

---

# 🔹 **5. MySQL Database**

(use official image, no Dockerfile).

---

# 🔹 **6. docker-compose.yml**

```yaml
version: "3.9"

services:
  nginx:
    build: ./nginx
    container_name: reverse-proxy
    ports:
      - "80:80"
      - "443:443"
    depends_on:
      - frontend
      - backend

  frontend:
    build: ./frontend
    container_name: react-frontend
    expose:
      - "3000"

  backend:
    build: ./backend
    container_name: node-backend
    expose:
      - "5000"
    environment:
      - DB_HOST=mysql
      - DB_USER=myuser
      - DB_PASSWORD=mypass
      - DB_NAME=mydb
    depends_on:
      - mysql

  mysql:
    image: mysql:8.0
    container_name: mysql-db
    environment:
      MYSQL_ROOT_PASSWORD: rootpass
      MYSQL_DATABASE: mydb
      MYSQL_USER: myuser
      MYSQL_PASSWORD: mypass
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql

volumes:
  mysql_data:
```

---

# ✅ How to Use

1. Generate certs (`nginx/certs/nginx.crt` and `nginx/certs/nginx.key`)
2. Start services:

   ```bash
   docker-compose up -d
   ```
3. Visit:

   * `https://localhost/` → React frontend
   * `https://localhost/api/` → Node backend

⚠️ Browser will warn about **self-signed cert** → safe to ignore in dev.
For **production**, you can integrate [Let’s Encrypt](https://letsencrypt.org/) via Certbot or a companion container.

---

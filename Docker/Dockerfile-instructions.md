Here's the **complete list of Dockerfile instructions** in their **recommended execution order**, with brief explanations:

---

### **1. Parser Directives** (Optional)
```dockerfile
# syntax=docker/dockerfile:1  # Specifies Dockerfile version
# escape=\                   # Sets escape character (default: \)
```

---

### **2. Base Image**
```dockerfile
FROM [image:tag]             # Sets the base image (e.g., `python:3.9-slim`)
```

---

### **3. Metadata & Configuration**
```dockerfile
LABEL key="value"            # Adds metadata (replaces deprecated MAINTAINER)
ARG VAR_NAME=default_value   # Build-time variable (override with `--build-arg`)
ENV VAR_NAME=value           # Runtime environment variable
WORKDIR /path                # Sets working directory
USER username                # Switches user (default: root)
```

---

### **4. Filesystem Operations**
```dockerfile
COPY src dest                # Copies local files (preferred for clarity)
ADD src dest                 # Copies + auto-extracts archives/URLs
VOLUME ["/data"]             # Declares external mount points
```

---

### **5. Dependency Installation**
```dockerfile
RUN command                  # Executes commands during build (e.g., `apt-get install`)
```

---

### **6. Ports & Healthchecks**
```dockerfile
EXPOSE 8080                  # Documents container ports (does not publish them)
HEALTHCHECK [options] CMD    # Defines container health test
```

---

### **7. Execution Control**
```dockerfile
ENTRYPOINT ["executable"]    # Container's main process (hard to override)
CMD ["arg1", "arg2"]         # Default arguments (can be overridden at runtime)
```

---

### **8. Triggers**
```dockerfile
ONBUILD instruction          # Instructions to run when used as a base image
```

---

### **Example Dockerfile Structure**
```dockerfile
# Syntax (optional)
# syntax=docker/dockerfile:1

# Base image
FROM ubuntu:22.04

# Metadata
LABEL maintainer="team@example.com"
ARG APP_VERSION=1.0
ENV NODE_ENV=production

# Filesystem
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .

# Networking
EXPOSE 3000
HEALTHCHECK --interval=30s CMD curl -f http://localhost:3000/health || exit 1

# Runtime
USER node
CMD ["npm", "start"]
```

---

### **Key Ordering Rules**:
1. **FROM** must be first (after optional syntax directives).  
2. **RUN/COPY** should be ordered from least to most frequently changing for cache optimization.  
3. **CMD/ENTRYPOINT** should be last.  

For official documentation: [Dockerfile Reference](https://docs.docker.com/engine/reference/builder/).
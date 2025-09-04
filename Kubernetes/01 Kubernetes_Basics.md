
---

# 🚀 **Docker Swarm vs Kubernetes (K8s)**

### 🔹 **Docker Swarm**

* **Definition:** A lightweight container orchestration tool built into Docker.
* **Best For:** Small to medium microservices applications.

**Limitations:**

* ❌ **No Auto Scaling** – Cannot scale automatically based on demand.
* ❌ **No Self-Healing** – Failed containers don’t restart automatically.
* ❌ **Not for Large Deployments** – Works fine for small setups, but not enterprise-grade.

---

### 🔹 **Kubernetes (K8s)**

* **Definition:** A powerful, open-source container orchestration platform.
* **Analogy:** Like a **pilot/governor** that manages containers.
* **Best For:** Large-scale, production-grade deployments.

**Key Functions:**

1. **Automatic Provisioning** – Creates and runs containers.
2. **Scaling (Up/Down)** – Adjusts container count automatically.
3. **Load Balancing** – Spreads traffic across containers.
4. **Self-Healing** – Restarts failed pods automatically.
5. **Update & Rollback** – Deploy updates and roll back if needed.
6. **Resource Management** – Optimizes CPU, memory, and storage.

---

# 🏗 **Kubernetes Architecture**

Kubernetes has **two main parts**:

### 1. **Control Plane (Master Components)**

The “brain” of Kubernetes. Manages the entire cluster.

* **API Server** → Front door of the cluster; receives commands (`kubectl`).
* **Scheduler** → Decides which node runs a pod.
* **Controller Manager** → Maintains desired state (e.g., restarts failed pods).
* **etcd** → Key-value database storing cluster state & config.

---

### 2. **Nodes (Worker Components)**

The “workers” where applications actually run.

* **Kubelet** → Ensures containers are running as instructed.
* **Kube-Proxy** → Handles networking & service communication.
* **Container Runtime** → Runs containers (Docker, containerd, CRI-O).
* **Pods** → Smallest deployable unit (can hold 1+ containers).

---

# 🛠 **Kubernetes Command-Line Tools**

* **kubectl** → Main CLI tool to interact with cluster.

  * Example: `kubectl get pods`, `kubectl apply -f deployment.yaml`
* **eksctl** → AWS-specific CLI for managing EKS clusters.
* **kubeadm** → Bootstraps Kubernetes clusters.

  * Example: `kubeadm init`, `kubeadm join`
* **Kubernetes Dashboard** → Web-based GUI for cluster management.

---

# 📦 **Core Kubernetes Objects**

### 🔹 **Pods**

* Encapsulation of **containers, storage, and network**.
* **Smallest unit** in Kubernetes.
* Usually **1 container per pod**, but can have multiple tightly-coupled containers.

---

### 🔹 **Pod Controllers** (to manage pods at scale)

* **Deployment** → For stateless apps (default).
* **DaemonSet** → Ensures 1 pod per node (e.g., monitoring/logging).
* **StatefulSet** → For stateful apps (databases, need persistent storage).

---

### 🔹 **Services** (expose pods to network)

* **ClusterIP** → Internal communication within cluster (default).
* **NodePort** → Expose service on each node’s IP at a static port.
* **LoadBalancer** → Integrates with cloud LB (AWS ELB, Azure LB, etc.).
* **ExternalName** → Maps service to external DNS name.
* **Headless Service** → No cluster IP, used for direct pod-to-pod access.
* **Ingress** → Exposes HTTP/HTTPS routes via domain names.

---

### 🔹 **Namespaces**

* Virtual clusters inside Kubernetes.
* Used to separate environments (e.g., **dev**, **test**, **prod**).

---

### 🔹 **DNS (CoreDNS / kube-dns)**

* Provides **service discovery** (pods communicate using service names instead of IPs).

---

### 🔹 **Storage**

* **Persistent Volume (PV):** Actual storage resource (disk).
* **Persistent Volume Claim (PVC):** Request for storage by pods.

---

# 📊 **Kubernetes Limits**

* **Max pods per node** → 110
* **Max nodes per cluster** → 5,000
* **Max pods per cluster** → 150,000
* **Max containers per cluster** → 300,000

---

# ✅ **Summary**

* **Docker Swarm** → Easy but limited. Good for small projects.
* **Kubernetes** → Complex but powerful. Best for production & enterprise.
* **Control Plane** = Brain, **Nodes** = Workers.
* **kubectl** = Remote control, **kubeadm** = Cluster setup.
* **Pods → Controllers → Services → Storage** = Kubernetes lifecycle.

---

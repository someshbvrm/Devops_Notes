
---

## 🔹 Core Components

* **API Server** – Central control plane, handles all kubectl/REST requests.
* **etcd** – Stores cluster state, configs, and secrets in a distributed key-value database.
* **Controller Manager** – Runs controllers like Node, Replication, and Endpoint controllers.
* **Scheduler** – Decides which node each pod runs on, based on resources and constraints.
* **Kubelet** – Node agent that ensures containers are running and healthy.
* **Kube Proxy** – Handles cluster networking and load balances traffic between pods.
* **Pod** – Smallest deployable unit containing one or more containers.
* **Node** – Worker machine (VM/physical) that runs pods.
* **Cluster** – Group of nodes controlled by a single Kubernetes control plane.
* **Namespace** – Logical separation for isolating workloads and resources.

---

## 🔹 Add-on Components

* **CoreDNS** – Internal DNS for pod-to-pod and service discovery.
* **Dashboard** – Web-based UI to monitor and manage cluster resources.
* **Ingress Controller** – Manages HTTP/HTTPS access to services.
* **Metrics Server** – Collects pod and node resource metrics for autoscaling.
* **Helm** – Package manager for Kubernetes using charts.
* **CNI Plugins** – Provide pod networking across nodes (Calico, Flannel, Cilium).
* **Logging & Monitoring Tools** – Add-ons like Prometheus, Grafana, EFK/ELK.

---

## 🔹 Probes (Pod Health Checks)

* **Liveness Probe** – Restarts containers if they become unresponsive.
* **Readiness Probe** – Ensures a pod only receives traffic when ready.
* **Startup Probe** – Checks if the container has successfully started.

---

## 🔹 Affinity & Anti-Affinity Rules

* **Node Affinity** – Schedules pods onto specific nodes based on labels.
* **Pod Affinity** – Co-locates pods on the same node for better performance.
* **Node Anti-Affinity** - 
* **Pod Anti-Affinity** – Spreads pods across nodes for high availability.
* **Taints and Tolerations** – Prevent or allow pods to be scheduled on certain nodes.

---

## 🔹 Workload Controllers

* **Deployment** – Manages stateless apps with scaling and updates.
* **ReplicaSet** – Ensures a defined number of pod replicas are running.
* **StatefulSet** – Manages stateful apps with stable network IDs and storage.
* **DaemonSet** – Runs one pod copy per node (e.g., logging/monitoring agents).
* **Job** – Runs a task to completion.
* **CronJob** – Runs jobs on a schedule (like cron in Linux).

---

## 🔹 Services & Networking

* **ClusterIP** – Internal-only service.
* **NodePort** – Exposes service on each node’s IP.
* **LoadBalancer** – Exposes service externally with cloud load balancer.
* **ExternalName** – Maps service to external DNS name.
* **Ingress** – Defines HTTP/HTTPS routing rules.
* **Network Policies** – Control which pods/services can talk to each other.
* **Service Mesh (Istio/Linkerd)** – Advanced traffic control, security, observability.

---

## 🔹 Storage & Data

* **Persistent Volume (PV)** – Actual storage in the cluster (e.g., EBS, NFS).
* **Persistent Volume Claim (PVC)** – A request for storage by a pod.
* **StorageClass** – Defines types of storage (fast, slow, SSD, HDD).
* **ConfigMap** – Stores non-secret config data for pods.
* **Secret** – Stores sensitive data like passwords, tokens, and keys.
* **CSI (Container Storage Interface)** – Standard for connecting external storage systems.

---

## 🔹 Security & Access Control

* **RBAC (Role-Based Access Control)** – Grants permissions to users, groups, and service accounts.
* **Service Account** – Provides identity for pods to interact with the API server.
* **Network Policies** – Restrict communication between pods/namespaces.
* **Pod Security Standards (PSS)** – Define pod-level security policies.
* **Admission Controllers** – Intercept requests to enforce policies.

---

## 🔹 Scaling & Reliability

* **Horizontal Pod Autoscaler (HPA)** – Scales pods based on CPU/memory usage.
* **Vertical Pod Autoscaler (VPA)** – Adjusts pod resource requests/limits automatically.
* **Cluster Autoscaler** – Adjusts the number of nodes in the cluster.
* **Resource Quotas** – Limits CPU/memory usage per namespace/project.
* **LimitRange** – Sets default or max/min resource limits per pod/container.

---

## 🔹 Observability

* **Events** – Record of what’s happening inside the cluster.
* **Logs** – Application and system logs from pods/nodes.
* **Metrics** – Performance data collected by Metrics Server, Prometheus, etc.
* **Tracing** – Distributed tracing with Jaeger or Zipkin.

---


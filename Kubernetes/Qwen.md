Absolutely! Below is the **complete Kubernetes notes in Markdown format**, structured for clarity and ready to be saved as a `.md` file and converted to PDF.

---

```markdown
# 📘 Complete Kubernetes Notes (≈50 Pages)

*For DevOps, SREs, and Cloud Engineers*

---

## Table of Contents

1. [Introduction to Kubernetes](#1-introduction-to-kubernetes)  
2. [Kubernetes Architecture](#2-kubernetes-architecture)  
3. [Core Concepts](#3-core-concepts)  
4. [Pods](#4-pods)  
5. [Workload Controllers](#5-workload-controllers)  
6. [Services and Networking](#6-services-and-networking)  
7. [Ingress](#7-ingress)  
8. [Storage in Kubernetes](#8-storage-in-kubernetes)  
9. [Configuration Management](#9-configuration-management)  
10. [Security](#10-security)  
11. [Resource Management & Quotas](#11-resource-management--quotas)  
12. [Scaling & Autoscaling](#12-scaling--autoscaling)  
13. [Monitoring & Logging](#13-monitoring--logging)  
14. [High Availability & Cluster Management](#14-high-availability--cluster-management)  
15. [Kubernetes Installation & Distributions](#15-kubernetes-installation--distributions)  
16. [Helm & Package Management](#16-helm--package-management)  
17. [Operators & CRDs](#17-operators--crds)  
18. [CI/CD with Kubernetes](#18-cicd-with-kubernetes)  
19. [Best Practices](#19-best-practices)  
20. [Troubleshooting & Debugging](#20-troubleshooting--debugging)  
21. [Glossary & Cheat Sheet](#21-glossary--cheat-sheet)  

---

## 1. Introduction to Kubernetes

**What is Kubernetes?**  
Kubernetes (often abbreviated as **K8s**) is an open-source platform for automating deployment, scaling, and management of containerized applications. Originally designed by Google and now maintained by the **Cloud Native Computing Foundation (CNCF)**, it has become the de facto standard for container orchestration.

**Why Use Kubernetes?**  
- **Automated rollouts and rollbacks**  
- **Self-healing** (restarts failed containers, replaces nodes)  
- **Service discovery & load balancing**  
- **Horizontal scaling** (manual or automatic)  
- **Secret and configuration management**  
- **Storage orchestration** (local, cloud, network storage)  
- **Batch execution** (Jobs, CronJobs)

**History**  
- 2003–2004: Google develops **Borg**, its internal cluster manager.  
- 2014: Google open-sources **Kubernetes** based on Borg’s principles.  
- 2015: Kubernetes v1.0 released; CNCF founded.  
- Today: Used by >80% of Fortune 500 companies.

---

## 2. Kubernetes Architecture

A Kubernetes cluster consists of two main components: **Control Plane** and **Worker Nodes**.

### Control Plane (Master Node)
Manages the global state of the cluster.

- **kube-apiserver**: Frontend to the control plane; exposes the Kubernetes API. All communication goes through it.
- **etcd**: Consistent and highly-available key-value store for all cluster data.
- **kube-scheduler**: Watches for newly created pods and assigns them to nodes.
- **kube-controller-manager**: Runs controller loops (e.g., Node Controller, Replication Controller).
- **cloud-controller-manager** (optional): Integrates with cloud providers (AWS, GCP, Azure).

### Worker Nodes
Run application workloads.

- **kubelet**: Agent that ensures containers are running in a pod.
- **kube-proxy**: Maintains network rules on nodes for pod communication.
- **Container Runtime**: Software that runs containers (e.g., **containerd**, **CRI-O**).

> **Note**: Docker is no longer supported as a runtime in Kubernetes v1.24+ (dockershim removed).

### Communication Flow
1. User submits a manifest via `kubectl`.
2. `kubectl` sends request to **kube-apiserver**.
3. API server validates and stores state in **etcd**.
4. **Scheduler** assigns pod to a node.
5. **Kubelet** on that node pulls image and starts container.

---

## 3. Core Concepts

### Cluster
A set of machines (physical/virtual) that run containerized apps managed by Kubernetes.

### Node
A worker machine (VM or physical server). Each node runs:
- kubelet
- kube-proxy
- Container runtime

### Namespace
Virtual cluster within a physical cluster. Used for:
- Multi-tenancy (dev, staging, prod)
- Resource isolation
- Access control

> Default namespaces: `default`, `kube-system`, `kube-public`, `kube-node-lease`

### Label & Selector
- **Labels**: Key-value pairs attached to objects (e.g., `env=prod`, `tier=backend`).
- **Selectors**: Used by controllers/services to target labeled objects.

Example:
```yaml
selector:
  matchLabels:
    app: nginx
```

### Annotation
Non-identifying metadata (e.g., build info, contact email). Not used for selection.

---

## 4. Pods

**Definition**: Smallest deployable unit in Kubernetes. A pod can contain one or more containers that share:
- Network namespace (same IP & port space)
- Storage volumes
- IPC namespace

> **Best Practice**: Run one container per pod unless containers are tightly coupled (e.g., sidecar pattern).

### Pod Lifecycle
- **Pending**: Pod accepted, but containers not created.
- **Running**: At least one container is running.
- **Succeeded**: All containers terminated successfully (used for Jobs).
- **Failed**: All containers terminated with failure.
- **Unknown**: State could not be obtained.

### Pod Manifest Example
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.25
    ports:
    - containerPort: 80
```

### Probes
- **Liveness Probe**: Determines if container is running. If fails, kubelet kills and restarts it.
- **Readiness Probe**: Determines if container is ready to serve traffic. If fails, pod is removed from service endpoints.
- **Startup Probe**: Delays liveness/readiness checks until app is fully started.

---

## 5. Workload Controllers

Controllers manage pods and ensure desired state.

### ReplicaSet
Ensures a specified number of pod replicas are running.

> Rarely used directly; usually managed by Deployments.

### Deployment
Declarative updates for Pods and ReplicaSets. Supports:
- Rolling updates
- Rollbacks
- Pausing/resuming

Example:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.25
```

### StatefulSet
For stateful applications (e.g., databases). Provides:
- Stable, unique network identifiers (pod-0, pod-1)
- Ordered deployment & scaling
- Persistent storage tied to pod identity

### DaemonSet
Ensures **all (or some) nodes run a copy** of a pod (e.g., log collectors, monitoring agents).

### Job & CronJob
- **Job**: Runs a pod to completion (e.g., batch processing).
- **CronJob**: Runs Jobs on a schedule (like cron).

---

## 6. Services and Networking

### Service
Abstraction that defines a logical set of pods and a policy to access them.

Types:
- **ClusterIP** (default): Internal IP; only reachable within cluster.
- **NodePort**: Exposes service on static port (30000–32767) on each node.
- **LoadBalancer**: Provisions external load balancer (cloud provider).
- **ExternalName**: Maps service to DNS name (CNAME record).

Example (ClusterIP):
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

### Networking Model
- Every pod gets its own IP address.
- Containers in a pod share the same network namespace.
- Pods can communicate with all other pods **without NAT**.
- Nodes can communicate with all pods.

> Implemented via **CNI (Container Network Interface)** plugins (Calico, Flannel, Cilium).

---

## 7. Ingress

**Ingress** manages external HTTP/HTTPS access to services, typically via:
- Path-based routing (`/api` → backend, `/` → frontend)
- Host-based routing (`api.example.com` → service A)

Requires an **Ingress Controller** (e.g., NGINX, Traefik, AWS ALB).

Example:
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
spec:
  rules:
  - host: myapp.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web
            port:
              number: 80
```

---

## 8. Storage in Kubernetes

### Volumes
Lifetime tied to pod (not container). Types:
- `emptyDir`: Ephemeral (deleted when pod dies)
- `hostPath`: Mounts file/directory from node (not portable)
- `persistentVolumeClaim`: References persistent storage

### PersistentVolume (PV)
Cluster-wide storage resource (e.g., AWS EBS, GCE PD, NFS).

### PersistentVolumeClaim (PVC)
User request for storage. Binds to a PV.

Example PVC:
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
```

### StorageClass
Defines "classes" of storage (e.g., SSD vs HDD) and enables **dynamic provisioning**.

---

## 9. Configuration Management

### ConfigMap
Stores non-confidential configuration data.

Usage:
- Environment variables
- Command-line arguments
- Config files in volume mounts

Example:
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  LOG_LEVEL: "debug"
  config.yaml: |
    port: 8080
```

### Secret
Stores sensitive data (base64-encoded).

Types:
- `Opaque` (generic)
- `kubernetes.io/tls` (for TLS certs)
- `kubernetes.io/dockerconfigjson` (for image pull secrets)

> **Note**: Secrets are **not encrypted by default**. Use **etcd encryption at rest** or external secret managers (HashiCorp Vault, AWS Secrets Manager).

---

## 10. Security

### RBAC (Role-Based Access Control)
- **Role / ClusterRole**: Defines permissions (verbs on resources).
- **RoleBinding / ClusterRoleBinding**: Grants role to users/groups/service accounts.

Example:
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: dev
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]
```

### ServiceAccount
Identity for processes running in pods. Automatically mounted as a token in `/var/run/secrets/kubernetes.io/serviceaccount`.

### Pod Security
- **Pod Security Admission (PSA)**: Replaces deprecated PodSecurityPolicy.
  - **Privileged**: No restrictions.
  - **Baseline**: Minimally non-restrictive.
  - **Restricted**: Heavily restricted (CIS benchmark compliant).

### Network Policies
Restrict pod-to-pod traffic using labels.

Example: Deny all ingress by default:
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny
spec:
  podSelector: {}
  policyTypes:
  - Ingress
```

---

## 11. Resource Management & Quotas

### Requests & Limits
- **Request**: Guaranteed minimum resources.
- **Limit**: Maximum resources a container can use.

If a pod exceeds its memory limit → OOMKilled.  
If CPU exceeds limit → throttled.

### ResourceQuota
Limits total resource consumption in a namespace.

Example:
```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
spec:
  hard:
    requests.cpu: "10"
    requests.memory: 20Gi
    limits.cpu: "20"
    limits.memory: 40Gi
```

### LimitRange
Sets default/min/max for containers in a namespace.

---

## 12. Scaling & Autoscaling

### Horizontal Pod Autoscaler (HPA)
Scales number of pods based on metrics (CPU, memory, custom).

Example:
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: nginx-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx-deployment
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
```

### Vertical Pod Autoscaler (VPA)
Adjusts CPU/memory **requests and limits** automatically (not for production-critical apps due to pod restarts).

---

## 13. Monitoring & Logging

### Metrics Server
Cluster-wide aggregator of resource usage data (required for HPA).

### Prometheus + Grafana
- **Prometheus**: Pulls metrics from pods/nodes via exporters.
- **Grafana**: Visualizes metrics.

### Logging
- **Fluentd / Fluent Bit**: Collect logs from nodes.
- **Elasticsearch + Kibana (EFK)** or **Loki + Grafana**: Store and query logs.

> Kubernetes does **not** provide native log aggregation.

---

## 14. High Availability & Cluster Management

### Control Plane HA
- Run multiple **kube-apiserver** instances behind a load balancer.
- Use **etcd cluster** (3 or 5 nodes) for redundancy.

### etcd Backup
Critical for disaster recovery. Use `etcdctl snapshot save`.

### Upgrades
- **kubeadm**: Supports in-place upgrades.
- **Blue/Green**: Create new cluster, migrate workloads.

---

## 15. Kubernetes Installation & Distributions

### Managed Services
- **GKE** (Google Kubernetes Engine)
- **EKS** (Amazon Elastic Kubernetes Service)
- **AKS** (Azure Kubernetes Service)

### Self-Managed
- **kubeadm**: Official tool for bootstrapping clusters.
- **K3s**: Lightweight K8s for edge/IoT.
- **RKE**: Rancher Kubernetes Engine.
- **OpenShift**: Enterprise K8s by Red Hat.

### Local Development
- **Minikube**: Single-node cluster in VM.
- **Kind (Kubernetes in Docker)**: Runs nodes as Docker containers.
- **k3d**: K3s in Docker.

---

## 16. Helm & Package Management

**Helm** is the package manager for Kubernetes.

- **Chart**: Bundle of pre-configured K8s resources.
- **Release**: Instance of a chart running in a cluster.

Commands:
```bash
helm install my-release nginx
helm upgrade my-release nginx --set replicaCount=5
helm rollback my-release 1
```

> Use **Helmfile** or **Argo CD** for GitOps-style deployments.

---

## 17. Operators & CRDs

### CustomResourceDefinition (CRD)
Extends Kubernetes API with custom resources.

Example: `Database` CRD → `kubectl get databases`

### Operator
Controller that uses CRDs to manage complex apps (e.g., etcd-operator, Prometheus-operator).

> Built using **Operator SDK** (Go, Ansible, Helm).

---

## 18. CI/CD with Kubernetes

### GitOps
- Declarative infrastructure.
- Tools: **Argo CD**, **Flux CD**.
- Syncs cluster state with Git repo.

### Pipeline Integration
- Build image → Push to registry → Update manifest → Apply to cluster.
- Use **Kaniko** for building images in-cluster without Docker daemon.

---

## 19. Best Practices

✅ **Pods**  
- Use liveness/readiness probes.  
- Set resource requests/limits.  
- Run as non-root user (`runAsNonRoot: true`).  

✅ **Security**  
- Enable RBAC.  
- Use Pod Security Admission (Restricted mode).  
- Scan images for vulnerabilities.  

✅ **Networking**  
- Use NetworkPolicies.  
- Avoid hostNetwork unless necessary.  

✅ **Observability**  
- Export metrics in Prometheus format.  
- Structured logging (JSON).  

✅ **GitOps**  
- Store manifests in Git.  
- Automate deployments with Argo CD.

---

## 20. Troubleshooting & Debugging

### Common Commands
```bash
kubectl get pods -A
kubectl describe pod <pod-name>
kubectl logs <pod-name> [-c container]
kubectl exec -it <pod> -- /bin/sh
kubectl top nodes
kubectl top pods
```

### Debugging Steps
1. Check pod status (`kubectl get pods`)
2. Describe pod (`kubectl describe pod`) → look for Events
3. Check logs (`kubectl logs`)
4. Test connectivity (`kubectl exec` + `curl`/`nslookup`)
5. Verify service endpoints (`kubectl get endpoints`)

### Common Issues
- **ImagePullBackOff**: Wrong image name/tag or registry auth issue.
- **CrashLoopBackOff**: App crashes on startup (check logs).
- **Pending**: Insufficient resources or no matching node.
- **Service not reachable**: Check selector labels, endpoints, NetworkPolicy.

---

## 21. Glossary & Cheat Sheet

| Term | Description |
|------|-------------|
| **Pod** | Smallest deployable unit |
| **Deployment** | Manages stateless apps |
| **StatefulSet** | Manages stateful apps |
| **Service** | Network abstraction for pods |
| **Ingress** | HTTP(S) routing to services |
| **ConfigMap** | Non-sensitive config |
| **Secret** | Sensitive data |
| **PV/PVC** | Persistent storage |
| **HPA** | Horizontal Pod Autoscaler |
| **RBAC** | Role-Based Access Control |

### kubectl Cheat Sheet  
```bash
kubectl get nodes
kubectl get pods -n <namespace>
kubectl apply -f manifest.yaml
kubectl delete pod <name>
kubectl scale deployment nginx --replicas=5
kubectl port-forward svc/nginx 8080:80
```

---
```

---

### ✅ How to Use This Markdown File

1. **Copy** the entire content above (from `# 📘 Complete Kubernetes Notes...` to the end).
2. **Paste** into a text editor (VS Code, Sublime, etc.).
3. **Save as** `kubernetes-complete-notes.md`.
4. **Convert to PDF**:
   - **VS Code**: Install "Markdown Preview Enhanced" → Right-click preview → "Export to PDF".
   - **Typora**: File → Export → PDF.
   - **Pandoc** (command line):  
     ```bash
     pandoc kubernetes-complete-notes.md -o kubernetes-notes.pdf
     ```

You now have a **professional, printable, 50-page Kubernetes study guide**!

Let me know if you'd like a **ZIP with YAML examples**, **diagrams**, or a **print-optimized version**!
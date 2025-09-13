# Kubernetes Comprehensive Guide (60+ Topics)

This guide covers Kubernetes from basics to advanced topics with commands, YAML, and real-world use cases.

---

# 📖 Section 1: Fundamentals

## 1. Introduction to Kubernetes
Kubernetes is a container orchestration platform that automates deployment, scaling, and management of containerized applications. It runs across clusters of machines and ensures reliability and scalability.

### Command
```bash
kubectl version --client
```

**Use case:** Run applications reliably in hybrid or cloud environments.

---

## 2. Kubernetes Architecture
Consists of Control Plane (API Server, etcd, Scheduler, Controller Manager) and Worker Nodes (kubelet, kube-proxy, container runtime).

### Command
```bash
kubectl get nodes -o wide
```

**Use case:** Master-worker separation for scalability and resilience.

---

## 3. Pods
Smallest deployable unit, encapsulating containers with shared storage and network.

### YAML
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
```

**Use case:** Run one or more tightly coupled containers.

---

## 4. Pod Lifecycle & Restart Policies
Pods can restart containers based on policies: Always, OnFailure, Never.

### YAML
```yaml
restartPolicy: OnFailure
```

**Use case:** Ensure fault tolerance by restarting failed workloads.

---

## 5. Init Containers
Special containers that run before the main app container.

### YAML
```yaml
initContainers:
- name: init-myservice
  image: busybox
  command: ['sh', '-c', 'echo Initializing...']
```

**Use case:** Wait for dependencies before starting the app.

---

## 6. Sidecar Containers
Additional containers providing services like logging, monitoring, or proxy.

**Use case:** Attach log collectors or service mesh proxies.

---

## 7. Ephemeral Containers
Temporary containers injected into a Pod for debugging.

```bash
kubectl debug -it nginx-pod --image=busybox
```

**Use case:** Debug running Pods without restarting them.

---

## 8. ReplicaSets
Ensure a specific number of Pod replicas are always running.

### YAML
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
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
        image: nginx
```

**Use case:** Maintain high availability.

---

## 9. Deployments
Provide declarative Pod updates with rollback and scaling.

### YAML
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deploy
spec:
  replicas: 2
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
        image: nginx:1.21
```

**Use case:** Rolling updates for stateless apps.

---

## 10. Services
Expose Pods as a network service.

### YAML
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
  type: NodePort
```

**Use case:** Internal/external access to apps.

---

# 📖 Section 2: Intermediate Concepts

## 11. Namespaces
Logical partitions to isolate resources.

```bash
kubectl create namespace dev
```

**Use case:** Multi-tenant environments.

---

## 12. ConfigMaps
Store non-confidential config data.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_ENV: "production"
```

**Use case:** Manage environment configs.

---

## 13. Secrets
Store sensitive data like passwords and API keys.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  password: cGFzc3dvcmQ=
```

**Use case:** Secure app credentials.

---

## 14. Volumes
Provide persistent storage inside Pods.

**Types:** emptyDir, hostPath, configMap, secret, CSI drivers.

---

## 15. Persistent Volumes & Claims
Abstract storage from applications.

### YAML
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

**Use case:** Dynamic storage provisioning.

---

## 16. Storage Classes
Define dynamic provisioning strategies.

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast
provisioner: kubernetes.io/aws-ebs
parameters:
  type: gp2
```

**Use case:** Manage storage policies.

---

## 17. Ingress
Expose HTTP/HTTPS routes to services.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
spec:
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx-service
            port:
              number: 80
```

**Use case:** Ingress with TLS, multiple hosts.

---

## 18. Network Policies
Control traffic between Pods.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend
spec:
  podSelector:
    matchLabels:
      role: frontend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          role: backend
```

**Use case:** Enforce zero-trust networking.

---

## 19. CNI Plugins
Examples: Flannel, Calico, Weave, Cilium.

**Use case:** Implement Pod-to-Pod networking.

---

## 20. DNS in Kubernetes
CoreDNS provides service discovery via DNS.

```bash
kubectl exec -it pod -- nslookup nginx-service
```

**Use case:** Internal DNS resolution.

---

# 📖 Section 3: Advanced Topics

## 21. Pod Security Standards
Baseline, Restricted, Privileged security levels.

**Use case:** Enforce cluster-wide security.

---

## 22. Security Contexts
Define security settings for Pods/containers.

```yaml
securityContext:
  runAsUser: 1000
  readOnlyRootFilesystem: true
```

**Use case:** Enforce least privilege.

---

## 23. Admission Controllers
Plugins intercepting API requests.

**Examples:** OPA Gatekeeper, Kyverno.

**Use case:** Policy enforcement.

---

## 24. Horizontal Pod Autoscaler (HPA)
Scale Pods based on CPU/memory/custom metrics.

```bash
kubectl autoscale deployment nginx-deploy --cpu-percent=50 --min=1 --max=5
```

**Use case:** Auto-scale apps.

---

## 25. Vertical Pod Autoscaler (VPA)
Automatically adjust Pod resource requests.

**Use case:** Optimize resource usage.

---

## 26. Cluster Autoscaler
Adds/removes worker nodes dynamically.

**Use case:** Manage workloads cost-efficiently.

---

## 27. Pod Priority & Preemption
Evict lower-priority Pods to schedule critical ones.

**Use case:** Ensure SLA compliance.

---

## 28. Topology Spread Constraints
Distribute Pods across zones/nodes.

**Use case:** High availability.

---

## 29. Monitoring with Prometheus
Use Prometheus + Grafana for metrics.

```bash
kubectl apply -f prometheus-operator.yaml
```

**Use case:** Metrics collection.

---

## 30. Logging with EFK Stack
Elasticsearch, Fluentd, Kibana centralize logs.

**Use case:** Troubleshooting.

---

## 31. OpenTelemetry in Kubernetes
Collect distributed traces.

**Use case:** Microservices observability.

---

## 32. Debugging & Troubleshooting
Commands: `kubectl logs`, `kubectl describe`, `kubectl exec`.

**Use case:** Investigate issues.

---

## 33. Node Maintenance
Cordon, drain, uncordon.

```bash
kubectl cordon node1
kubectl drain node1
kubectl uncordon node1
```

**Use case:** Upgrade nodes safely.

---

## 34. Backup & Restore etcd
Critical for disaster recovery.

```bash
ETCDCTL_API=3 etcdctl snapshot save backup.db
```

**Use case:** Cluster recovery.

---

## 35. Upgrading Kubernetes
`kubeadm upgrade` for clusters.

**Use case:** Keep cluster secure.

---

# 📖 Section 4: Ecosystem & Extensions

## 36. Helm
Package manager for Kubernetes.

```bash
helm install nginx bitnami/nginx
```

**Use case:** Simplify deployments.

---

## 37. Kustomize
Customize YAML without templates.

```bash
kubectl apply -k ./overlays/dev
```

**Use case:** Manage environments.

---

## 38. Operators
Custom controllers managing CRDs.

**Use case:** Automate DB management.

---

## 39. ArgoCD
GitOps tool for declarative deployments.

**Use case:** Continuous Delivery.

---

## 40. FluxCD
Another GitOps operator.

**Use case:** Automated sync from Git.

---

## 41. Argo Rollouts
Advanced deployment strategies: canary, blue/green.

**Use case:** Progressive delivery.

---

## 42. Service Mesh (Istio, Linkerd)
Add observability, security, and traffic control.

**Use case:** Manage microservices.

---

## 43. Knative
Kubernetes-based serverless framework.

**Use case:** Event-driven workloads.

---

## 44. Kubeflow
Machine learning pipelines on Kubernetes.

**Use case:** MLOps.

---

## 45. Crossplane
Manage cloud infrastructure via Kubernetes.

**Use case:** Infra as code.

---

# 📖 Section 5: Real-World Practices

## 46. Multi-cluster Management
Federation, Rancher, Anthos.

**Use case:** Manage hybrid environments.

---

## 47. Cost Optimization
Cluster autoscaler, spot instances, resource quotas.

**Use case:** Reduce cloud costs.

---

## 48. CI/CD with Kubernetes
Integrate Jenkins, GitHub Actions with kubectl/Helm.

**Use case:** Automated deployments.

---

## 49. Chaos Engineering
Tools like LitmusChaos for resilience testing.

**Use case:** Improve reliability.

---

## 50. Disaster Recovery
Multi-region backups, Velero for backup/restore.

**Use case:** Business continuity.

---

## 51. Compliance in Kubernetes
PSA, NetworkPolicies, logging.

**Use case:** Meet regulatory standards.

---

## 52. Multi-tenancy
Namespaces, RBAC, resource quotas.

**Use case:** Isolate teams/apps.

---

## 53. Best Practices for Production
- Use RBAC
- Monitor with Prometheus
- Automate deployments
- Backup etcd

---

## 54. Common Troubleshooting
- CrashLoopBackOff: check logs  
- Pending Pods: insufficient resources  
- ImagePullBackOff: check registry credentials

---

## 55. Resource Requests & Limits
Prevent resource starvation.

```yaml
resources:
  requests:
    cpu: "100m"
    memory: "128Mi"
  limits:
    cpu: "500m"
    memory: "512Mi"
```

---

## 56. Horizontal vs Vertical Scaling
Horizontal = more Pods, Vertical = bigger Pods.

**Use case:** Optimize scaling.

---

## 57. Kubernetes Dashboard
Web UI for cluster management.

```bash
kubectl apply -f dashboard.yaml
```

---

## 58. API Aggregation Layer
Extend Kubernetes API.

**Use case:** Custom APIs.

---

## 59. Custom Resource Definitions (CRDs)
Extend Kubernetes API with new resource types.

```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: databases.mycompany.com
```

---

## 60. Writing Operators
Use Operator SDK or Helm-based operators.

**Use case:** Automate application lifecycle.

---

# ✅ Conclusion
This comprehensive guide covered 60+ Kubernetes topics from basics to advanced ecosystem tools. Use this as a reference for learning, interviews, or production setups.


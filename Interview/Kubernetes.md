
---

### **Category 1: Beginner Level Questions (1-10)**

**1. What is Kubernetes and why is it used?**

Kubernetes is an open-source container orchestration platform. The purpose of using Kubernetes is that it automates the deploying, scaling, and managing of containerized applications. It helps you manage multiple containers efficiently across different environments, like on-premise servers, cloud, or even hybrid (a combination of on-premise and cloud).

For example, let's consider a company that is running multiple microservices in Docker containers. Managing these manually is a very difficult task. So over here, Kubernetes comes into the picture to help you automate deployment, load balancing, and scaling.

**2. What are Pods in Kubernetes?**

A Pod is the smallest deployable unit available in Kubernetes. It contains one or more containers, and all of these containers share among each other a few things such as storage, network, and specifications for how the containers should run.

For example, let's consider that you have to deploy an Nginx web server in Kubernetes. This Nginx web server will run inside a Pod. A Pod can also have multiple containers working together, such as a web app and a logging container.

**3. What is a Kubernetes Node?**

A Kubernetes cluster consists of nodes (multiple nodes together form a cluster). A node is a physical machine (if on-premise) or a virtual machine (if in cloud) that runs containerized applications. Inside a node, you have containerized applications, and each node has components like the kubelet, a container runtime, and a network proxy.

For example, in a three-node cluster, each node will run different Pods. If one node fails, Kubernetes automatically shifts the workloads to other nodes.

**4. What is a Deployment in Kubernetes?**

A Deployment is a Kubernetes resource that manages the lifecycle of the Pods. With the help of a Deployment, you can define the desired state of an application, and this ensures that the application you're running is highly available. If any problem occurs, it is very easy to roll back the Deployment.

For example, if you deploy an application with three replicas using a Deployment, Kubernetes ensures that the three instances are always up and running.

**5. How does Kubernetes perform load balancing?**

Load balancing means balancing the traffic among different nodes. Kubernetes provides internal load balancing with the help of Services. A Service assigns a stable IP to a group of Pods and distributes the traffic among them.

For example, if you deploy a web application with three replicas, the Kubernetes Service routes the requests to different Pods based on availability.

**6. (Scenario) A Pod keeps crashing very frequently. How do you troubleshoot this?**

First, check the Pod logs using the command: `kubectl logs <pod-name>`
Next, describe the Pod events using: `kubectl describe pod <pod-name>`
Third, check the resource limits; the Pod may be exceeding CPU or memory limits.
Finally, view the container restart history using: `kubectl get pod <pod-name> -o wide`

**7. What is a ConfigMap in Kubernetes?**

A ConfigMap is an API object that allows you to store configuration data separately from the container images. This makes the applications more flexible. For example, instead of hard-coding the database credentials, you can store them in a ConfigMap and then mount them in a Pod.

**8. (Scenario) A Service is not reaching the correct Pods. How do you debug this?**

*   Check if the Pods are running: `kubectl get pods -o wide`
*   Verify the Service configuration is correct: `kubectl get service <service-name>`
*   Check the Network Policies to ensure smooth communication between Pods.
*   Describe the Service: `kubectl describe service <service-name>`

**9. What is the purpose of Namespaces in Kubernetes?**

Namespaces allow multiple teams or applications to use the same cluster without any conflict. Namespaces logically separate the resources. For example, a 'production' namespace and a 'development' namespace can host the same application without any interference.

**10. How do you scale a Kubernetes Deployment?**

You can scale a Kubernetes Deployment with the `kubectl scale` command:
`kubectl scale deployment <deployment-name> --replicas=5`
Alternatively, you can define the replica count in the YAML file.

---

### **Category 2: Intermediate Level Questions (11-20)**

**11. What is a StatefulSet in Kubernetes?**

A StatefulSet is a controller used to manage stateful applications. It ensures that each Pod has a unique and stable identity, persistent storage, and maintains the correct order of deployment and scaling. They are essential for applications like databases (MySQL, PostgreSQL), message brokers (Kafka, RabbitMQ), distributed caches (Redis), and search engines (Elasticsearch).

**12. How does Kubernetes handle rolling updates and rollbacks?**

Kubernetes uses rolling updates to deploy new versions gradually, minimizing downtime. If an update fails, it allows you to roll back to a previous stable version.
*   **Rollback command:** `kubectl rollout undo deployment/<deployment-name>`
*   **View history:** `kubectl rollout history deployment/<deployment-name>`
*   **Rollback to specific revision:** `kubectl rollout undo deployment/<deployment-name> --to-revision=2`

**13. What is a DaemonSet?**

A DaemonSet is a specialized controller that ensures a specific Pod runs on *all* nodes in a cluster. This is useful for running background services like logging agents (e.g., Fluentd) on every node. It automatically adds a Pod to new nodes and removes Pods from deleted nodes.

**14. (Scenario) A Pod is stuck in a Pending state. How do you debug this?**

A Pod remains Pending when it cannot be scheduled. Reasons and checks include:
*   **Node Capacity:** Check if nodes have enough resources: `kubectl describe nodes`
*   **Storage Constraints:** Check if Persistent Volume Claims (PVCs) are available.
*   **Pod Events:** View scheduling failures: `kubectl describe pod <pod-name>`
*   **Scheduler Logs:** Check the Kubernetes scheduler logs.
*   **Image Pull:** Ensure the container image can be pulled from the registry.
*   **Scheduling Constraints:** Check for node selectors, affinity rules, taints, and tolerations.
*   **Network:** Check for CNI plugin (Calico, Flannel) misconfigurations.

**15. How do you secure Kubernetes Secrets?**

Best practices for securing Secrets:
*   Use Kubernetes Secrets instead of hardcoding credentials.
*   Enable encryption at rest for Secrets in etcd.
*   Limit access using RBAC.
*   Use external secret management tools like HashiCorp Vault or AWS Secrets Manager.
*   Avoid storing secrets in environment variables.
*   Rotate secrets periodically.

**16. (Scenario) A deployment update has caused downtime. How do you prevent this?**

Use a rolling update strategy (instead of 'recreate') and configure the update process carefully. Set `maxUnavailable` to 1 to ensure at least some Pods are always running during the update.

**17. What is a Persistent Volume (PV) and Persistent Volume Claim (PVC)?**

Pods are ephemeral; data is lost when a Pod is deleted. PVs provide persistent storage at the cluster level. PVCs are requests for storage by a Pod. Storage Classes enable dynamic volume provisioning.

**18. How do you set up autoscaling in Kubernetes?**

Use the Horizontal Pod Autoscaler (HPA). Command:
`kubectl autoscale deployment <deployment-name> --cpu-percent=50 --min=2 --max=5`
This automatically scales the number of Pods based on CPU utilization (or other metrics).

**19. What is a Network Policy in Kubernetes?**

A Network Policy controls how Pods communicate with each other and other network resources. It defines ingress (incoming) and egress (outgoing) traffic rules. By default, all Pods can communicate freely. Network Policies are necessary to enforce security, restrict unauthorized access to sensitive services (like databases), and meet compliance requirements.

**20. (Scenario) How do you expose a Kubernetes application externally?**

| Method | Description | Best For |
| :--- | :--- | :--- |
| **LoadBalancer Service** | Exposes the app directly with a cloud-provided external IP. | Cloud-based clusters (AWS, GCP, Azure). |
| **NodePort Service** | Assigns a static port on each node for external access. | Small-scale or on-premises clusters. |
| **Ingress Controller** | Handles domain-based routing with HTTPS support. | Managing multiple services under one domain. |
| **External DNS / Service Mesh** | Provides advanced routing, service discovery, and security. | Large-scale production environments. |

---

### **Category 3: Experienced Level Questions (21-30)**

**21. How does Kubernetes handle node failures?**

Kubernetes ensures application availability using several mechanisms:
1.  **Node Controller Detection:** The kubelet sends heartbeats. If it stops for 5s, the node is marked 'Not Ready'; after 40s, it's removed.
2.  **Pod Rescheduling:** Affected Pods are automatically moved to healthy nodes.
3.  **Persistent Volume Handling:** PVs are detached from the failed node and reattached to the new node.
4.  **Self-healing with ReplicaSets:** If a Pod terminates, a new replica is started as defined in the Deployment.

**22. What are Custom Resource Definitions (CRDs)?**

CRDs extend the Kubernetes API, allowing users to define their own API objects (custom resources). This enables Kubernetes to manage new types of resources beyond built-in ones like Pods and Services, tailoring it to specific use cases.

**23. How do you implement GitOps in Kubernetes?**

Store Kubernetes manifests (YAML files) in a Git repository. Use a GitOps tool like Argo CD or Flux to automatically synchronize these configurations with the cluster. The tool will revert any manual changes made directly to the cluster, ensuring the cluster state always matches the Git repository state.

**24. How do you troubleshoot a memory leak in a Kubernetes application?**

*   Check Pod memory usage: `kubectl top pod --containers`
*   View container logs: `kubectl logs <pod-name>`
*   Describe the Pod for errors: `kubectl describe pod <pod-name>`
*   **Enable resource limits** in the YAML to prevent excessive consumption.
*   Use profiling tools like Prometheus/Grafana for monitoring, or heap dump/pprof for Go applications.

**25. What is a Kubernetes Operator?**

An Operator is an advanced custom controller that automates the lifecycle management of complex, stateful applications (e.g., databases, message queues). They use CRDs to define and automate application-specific tasks like backups, restoration, scaling, and self-healing, which are not handled natively by Kubernetes.

**26. How does Kubernetes RBAC work?**

Role-Based Access Control (RBAC) restricts and grants access to cluster resources based on user roles.
*   **Role / ClusterRole:** Defines permissions (e.g., what actions can be taken on Pods). Roles are namespace-scoped; ClusterRoles are cluster-scoped.
*   **RoleBinding / ClusterRoleBinding:** Binds a Role/ClusterRole to a user, group, or ServiceAccount.
*   **Subject:** The user, group, or ServiceAccount that gets the permissions.

**27. How do you secure Kubernetes clusters?**

Key security measures:
*   Enable RBAC to follow the principle of least privilege.
*   Use Network Policies to control Pod communication.
*   Secure the API server with authentication and authorization.
*   Scan container images for vulnerabilities.
*   Restrict privileged container execution and hostPath mounts.
*   Enable audit logging for the Kubernetes API.
*   Regularly update Kubernetes and its components.

**28. (Scenario) Your cluster is running slow. How do you optimize it?**

*   **Check Resource Usage:** Use `kubectl top nodes/pods`. Upgrade nodes or use the cluster autoscaler if usage is consistently high.
*   **Scale Workloads:** Use the Horizontal Pod Autoscaler (HPA) to dynamically scale Pods based on traffic.
*   **Optimize Networking:** Check for network latency. Use ClusterIP for internal services, optimize Ingress controllers, and tune CNI plugins.
*   **Remove Unused Resources:** Delete completed Jobs, old Pods, unused ConfigMaps, and Secrets to free up resources.

**29. How do you handle multi-cluster Kubernetes deployments?**

Best practices:
*   Use **Kubernetes Federation** (KubeFed) to manage multiple clusters from a single control plane.
*   Use **service meshes** (Istio, Linkerd) for secure cross-cluster communication.
*   Deploy applications using **GitOps tools** (Argo CD) for consistent, automated deployments across all clusters.
*   Synchronize secrets across clusters using **HashiCorp Vault**.

**30. What are Kubernetes admission controllers?**

Admission controllers are pluggable components that intercept requests to the Kubernetes API server *before* they are persisted to etcd. They can:
*   **Validate** requests (e.g., require specific labels).
*   **Mutate** requests (e.g., inject a sidecar container).
*   **Enforce** security policies and compliance.

Best practices:
*   Enable only necessary controllers.
*   Use **ValidatingWebhookConfiguration** and **MutatingWebhookConfiguration** for custom logic.
*   Implement policies with tools like OPA Gatekeeper or Kyverno.
*   Ensure webhooks are highly available.

---

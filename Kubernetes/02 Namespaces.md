
---

# 🔹 **Namespaces in Kubernetes – Short Notes**

* **Definition:**
  A **Namespace** is a way to divide a single Kubernetes cluster into multiple **virtual clusters**.

* **Purpose:**
  Helps in organizing and managing resources in **large environments** where many teams, projects, or environments share the same cluster.

---

### ✅ **Key Points**

* Each namespace has its own **pods, services, and deployments**.
* Resources inside one namespace are **isolated** from others (by default).
* Good for **multi-team** or **multi-project** clusters.
* **Default namespaces:**

  * `default` → Where objects are created if no namespace is specified.
  * `kube-system` → Contains system components (API server, DNS, etc.).
  * `kube-public` → Publicly readable data (e.g., cluster info).
  * `kube-node-lease` → Stores node heartbeat info.

---

### ✅ **Use Cases**

* Separate **dev, test, and prod** environments in one cluster.
* Resource quotas per team/project.
* Apply security boundaries (RBAC per namespace).

---

### ✅ **Common Commands**

* View namespaces:

  ```bash
  kubectl get namespaces
  ```
* Create a namespace:

  ```bash
  kubectl create namespace dev
  ```
* Deploy to a specific namespace:

  ```bash
  kubectl apply -f app.yaml -n dev
  ```
* Switch default namespace (with kubectl context):

  ```bash
  kubectl config set-context --current --namespace=dev
  ```

---

👉 **Simple Analogy:**
A **Kubernetes cluster = apartment building** 🏢
Each **namespace = separate flat** 🏠 (isolated, but inside the same building).

---

## **1. Namespace Creation & Management**

* **Create a namespace directly:**
  `kubectl create namespace <name>` → Creates a new namespace.
  *Example:* `kubectl create namespace dev`

* **Create a namespace using YAML:**
  `kubectl apply -f namespace.yaml` → Creates or updates namespace from YAML.
  *Example YAML:*

  ```yaml
  apiVersion: v1
  kind: Namespace
  metadata:
    name: qa
  ```

* **Delete a namespace:**
  `kubectl delete namespace <name>` → Deletes namespace and all resources inside.
  *Example:* `kubectl delete namespace dev`

* **List all namespaces:**
  `kubectl get namespaces` → Shows all namespaces with their status.

* **Describe a namespace:**
  `kubectl describe namespace <name>` → Shows details, labels, annotations, and quotas.
  *Example:* `kubectl describe namespace prod`

---

## **2. Working in a Specific Namespace**

* **Switch current context to a namespace:**
  `kubectl config set-context --current --namespace=<name>` → Makes all future commands operate in that namespace by default.
  *Example:* `kubectl config set-context --current --namespace=qa`

* **List all resources in a namespace:**
  `kubectl get all -n <namespace>` → Shows pods, deployments, services, etc., in a namespace.
  *Example:* `kubectl get all -n dev`

* **Deploy resources into a namespace using YAML:**
  `kubectl apply -f deployment.yaml -n <namespace>` → Deploys a resource in the specified namespace.
  *Example:* `kubectl apply -f nginx-deploy.yaml -n prod`

* **Delete resources in a namespace:**
  `kubectl delete -f deployment.yaml -n <namespace>` → Deletes the resource from the specified namespace.

---

## **3. Pods and Services in Namespaces**

* **List pods in a namespace:**
  `kubectl get pods -n <namespace>` → Lists pods only in that namespace.
  *Example:* `kubectl get pods -n dev`

* **Access a pod in a namespace:**
  `kubectl exec -it <pod-name> -n <namespace> -- bash` → Opens shell in the pod.
  *Example:* `kubectl exec -it nginx-pod-1 -n dev -- bash`

* **View logs of a pod in a namespace:**
  `kubectl logs <pod-name> -n <namespace>` → Retrieves logs from a pod.
  *Example:* `kubectl logs nginx-pod-1 -n prod`

* **Port-forward a pod/service in a namespace:**
  `kubectl port-forward <pod/service-name> <local-port>:<remote-port> -n <namespace>` → Access pod/service locally.
  *Example:* `kubectl port-forward svc/nginx 8080:80 -n dev`

---

## **4. Resource Quotas and Limits**

* **Set resource quotas in a namespace:**
  `kubectl apply -f resource-quota.yaml -n <namespace>` → Limits CPU, memory, and object counts.
  *Example YAML:*

  ```yaml
  apiVersion: v1
  kind: ResourceQuota
  metadata:
    name: dev-quota
  spec:
    hard:
      pods: "10"
      requests.cpu: "4"
      requests.memory: 8Gi
  ```

* **Set limit ranges in a namespace:**
  `kubectl apply -f limit-range.yaml -n <namespace>` → Sets default CPU/memory limits for pods.
  *Example YAML:*

  ```yaml
  apiVersion: v1
  kind: LimitRange
  metadata:
    name: dev-limits
  spec:
    limits:
    - default:
        cpu: "500m"
        memory: "512Mi"
      defaultRequest:
        cpu: "200m"
        memory: "256Mi"
      type: Container
  ```

---

## **5. RBAC and Access Control per Namespace**

* **List roles in a namespace:**
  `kubectl get roles -n <namespace>` → Shows all roles.

* **List role bindings in a namespace:**
  `kubectl get rolebindings -n <namespace>` → Shows role bindings assigning users/groups.

* **Create a role and role binding in a namespace:**
  *Example:* Create `dev-role.yaml` and `dev-rolebinding.yaml` to give a user access only to dev namespace.

---

## **6. Quick Real-Time Commands Summary**

| Command                                                | Purpose                           | Example                                                |
| ------------------------------------------------------ | --------------------------------- | ------------------------------------------------------ |
| `kubectl get ns`                                       | List namespaces                   | `kubectl get ns`                                       |
| `kubectl create ns dev`                                | Create namespace                  | `kubectl create ns dev`                                |
| `kubectl apply -f ns.yaml`                             | Create/update namespace from YAML | `kubectl apply -f dev-ns.yaml`                         |
| `kubectl delete ns dev`                                | Delete namespace                  | `kubectl delete ns dev`                                |
| `kubectl get all -n dev`                               | Get all resources in namespace    | `kubectl get all -n dev`                               |
| `kubectl exec -it pod -n dev -- bash`                  | Access pod shell                  | `kubectl exec -it nginx-pod -n dev -- bash`            |
| `kubectl logs pod -n dev`                              | Pod logs                          | `kubectl logs nginx-pod -n dev`                        |
| `kubectl port-forward svc/nginx 8080:80 -n dev`        | Port forward                      | `kubectl port-forward svc/nginx 8080:80 -n dev`        |
| `kubectl config set-context --current --namespace=dev` | Switch namespace                  | `kubectl config set-context --current --namespace=dev` |

---

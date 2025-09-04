Today we're focusing on a critical skill for any DevOps engineer: debugging applications in Kubernetes. We won't be covering cluster-level issues, but rather the common problems you'll face after deploying an application.

To effectively triage an issue, the first command you should always run is:
```bash
kubectl get pods -w
```
This shows the status of your pods, which is the starting point for all debugging. The pod's status indicates which phase of its lifecycle it's failing in.

A pod's creation has three main phases:
1.  **Scheduled:** The pod is assigned to a worker node.
2.  **Container Creation:** The container image is pulled and the container is created.
3.  **Running:** The startup command runs inside the container.

We will explore 13 common errors, grouped by the phase where they occur.

---

### Phase 1: Pod Stuck in `Pending` State
This means the pod cannot be scheduled onto a node.

**1. Insufficient Resources**
*   **Symptoms:** Pod status is `Pending`.
*   **Debug Command:**
    ```bash
    kubectl describe pod <pod-name>
    ```
*   **Cause & Fix:** The `Events` section in the output will show `Insufficient memory` or `Insufficient cpu`. This happens when no node has enough resources to meet the `requests` specified in your pod spec. Solution: Reduce the resource requests in your deployment, add more worker nodes, or configure cluster autoscaling.

**2. Node Affinity/Anti-Affinity Rules**
*   **Symptoms:** Pod status is `Pending`.
*   **Debug Command:**
    ```bash
    kubectl describe pod <pod-name>
    ```
*   **Cause & Fix:** The `Events` will indicate no node matched the pod's affinity/anti-affinity rules. Check the rules in your pod spec and ensure you have nodes with the required labels.

**3. Unbound PersistentVolumeClaim (PVC)**
*   **Symptoms:** Pod and PVC status are both `Pending`.
*   **Debug Command:**
    ```bash
    kubectl get pvc
    kubectl describe pod <pod-name>
    ```
*   **Cause & Fix:** The pod requires a PersistentVolume (PV) that is not available. The `Events` will show this. Solution: Ensure a PV is available or that your StorageClass can dynamically provision one.

**4. Node Taints**
*   **Symptoms:** Pod status is `Pending`.
*   **Debug Command:**
    ```bash
    kubectl describe pod <pod-name>
    kubectl describe node <node-name> | grep Taint
    ```
*   **Cause & Fix:** The `Events` will show that the node has an unignored taint. A node taint prevents pods from being scheduled unless they have a matching toleration. Solution: Add the correct `tolerations` to your pod spec.

---

### Phase 2: Pod Stuck in `ContainerCreating` State
The pod is scheduled, but the container cannot be created.

**5. Unavailable ConfigMap or Secret**
*   **Symptoms:** Pod status is `ContainerCreating` or `Pending`.
*   **Debug Command:**
    ```bash
    kubectl describe pod <pod-name>
    kubectl get configmap,secret
    ```
*   **Cause & Fix:** The `Events` will show a failure to find a ConfigMap or Secret that the pod mounts as a volume. Solution: Ensure the referenced ConfigMap or Secret exists in the same namespace.

---

### Phase 3: Pod in `CrashLoopBackOff` State
The container starts but crashes repeatedly.

**6. Application Crash (e.g., Out of Memory - OOM)**
*   **Symptoms:** Pod status is `CrashLoopBackOff`.
*   **Debug Command:**
    ```bash
    kubectl describe pod <pod-name>
    kubectl logs <pod-name> --previous
    ```
*   **Cause & Fix:** The `describe` command might show `OOMKilled`. The container is exceeding its memory `limit`. Check the logs from the previous crash (`--previous`) for application errors. Solution: Increase the memory `limits` or fix the application's memory leak.

**7. Failed Liveness Probe**
*   **Symptoms:** Pod status is `CrashLoopBackOff` after being `Running` for a short time.
*   **Debug Command:**
    ```bash
    kubectl describe pod <pod-name>
    kubectl logs <pod-name>
    ```
*   **Cause & Fix:** Kubernetes is restarting the container because its liveness probe is failing. Check the probe configuration (command, path, port) in your pod spec and the application logs to see why the health check is failing.

**8. Failing Init Container**
*   **Symptoms:** Pod status is `Init:CrashLoopBackOff`.
*   **Debug Command:**
    ```bash
    kubectl describe pod <pod-name>
    kubectl logs <pod-name> -c <init-container-name>
    ```
*   **Cause & Fix:** An init container must complete successfully before the main app container can start. Use the `logs` command to see why the specific init container is failing and fix its script or configuration.

**9. Application Runtime Error (e.g., Database Connection)**
*   **Symptoms:** Pod status is `CrashLoopBackOff`.
*   **Debug Command:**
    ```bash
    kubectl logs <pod-name>
    ```
*   **Cause & Fix:** The application itself is crashing on startup. The logs are the most important tool here. A common example is a connection error to a database or other dependent service. Solution: Fix the application configuration or ensure its dependencies are available.

---

### Other Common Scenarios

**10. ImagePullBackOff**
*   **Symptoms:** Pod status is `ImagePullBackOff`.
*   **Debug Command:**
    ```bash
    kubectl describe pod <pod-name>
    ```
*   **Cause & Fix:** The `Events` will show the reason Kubernetes can't pull the image (e.g., `ImageNotFound`, `ErrImagePull`, authentication issues for a private registry). Solution: Check the image name, tag, and pull secrets.

**11. Resource Quotas**
*   **Symptoms:** Fewer pods are running than requested in your deployment. No explicit pod error.
*   **Debug Command:**
    ```bash
    kubectl get deployment
    kubectl describe replicaset <replicaset-name>
    ```
*   **Cause & Fix:** A ResourceQuota object limits resources in a namespace. The `describe replicaset` output will show a message about violating quota. Solution: Modify the deployment's resource requests or adjust the namespace's resource quota.

**12. Service Routing Issues**
*   **Symptoms:** Pods are `Running` but the application is inaccessible via its Service.
*   **Debug Command:**
    ```bash
    kubectl get endpoints <service-name>
    kubectl describe service <service-name>
    ```
*   **Cause & Fix:** The `Endpoints` list for the Service will be empty if the Service's `selector` labels do not match the Pod's labels. Solution: Correct the labels in either the Service or the Pod spec so they match.

### Debugging Workflow Summary:
1.  **`kubectl get pods -w`**: Identify the pod's status.
2.  **`kubectl describe pod <pod-name>`**: Get detailed events and state reasons.
3.  **`kubectl logs <pod-name>`**: See what's happening inside the running container.
4.  **`kubectl logs <pod-name> --previous`**: See logs from the previous crashed container instance.
5.  Check related objects: **`kubectl get pvc, configmap, secret, endpoints, replicaset`**.
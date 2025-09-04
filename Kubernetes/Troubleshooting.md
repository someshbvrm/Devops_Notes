
---

# 🔹 Kubernetes Troubleshooting Matrix (Issue → Symptom → Cause → Fix)

| **Area**                   | **Symptom**                         | **Possible Cause**                                           | **Fix / Command**                                                            |
| -------------------------- | ----------------------------------- | ------------------------------------------------------------ | ---------------------------------------------------------------------------- |
| **Pods**                   | Pod stuck in `Pending`              | No node resources, wrong nodeSelector/taint, PVC not bound   | `kubectl describe pod`; check nodes/resources; fix taints & storage          |
|                            | Pod in `CrashLoopBackOff`           | App crash, wrong command, missing config, probe failure, OOM | `kubectl logs <pod> --previous`; check probes, fix image, increase resources |
|                            | Pod in `ImagePullBackOff`           | Wrong image, private registry w/o secret, DNS/network issue  | Verify image path; add `imagePullSecrets`; `kubectl exec busybox nslookup`   |
|                            | Pod in `OOMKilled`                  | Memory limit exceeded                                        | Increase memory in pod spec or optimize app                                  |
|                            | Pod stuck `ContainerCreating`       | PVC not bound, volume issue                                  | `kubectl get pvc,pv`; check storageClass & volume mounts                     |
| **Services / Networking**  | Pod-to-Pod communication fails      | Network policy, CNI issue                                    | Check network policies, CNI logs, test with `ping/curl`                      |
|                            | Service unreachable inside cluster  | Wrong label selector, no endpoints                           | `kubectl get endpoints <svc>`                                                |
|                            | Service unreachable outside cluster | NodePort/LoadBalancer misconfig, firewall                    | Check firewall rules, LB settings, Ingress                                   |
|                            | DNS resolution fails                | CoreDNS crash, wrong config                                  | Restart CoreDNS, check ConfigMap, `kubectl exec busybox nslookup`            |
| **Nodes**                  | Node in `NotReady`                  | Kubelet stopped, network failure, disk full                  | `systemctl status kubelet`; check disk, restart kubelet                      |
|                            | Pods not scheduling on node         | Node taints, insufficient resources, selector mismatch       | `kubectl describe node`; remove taints or add tolerations                    |
| **Storage**                | PVC stuck `Pending`                 | No PV, wrong StorageClass                                    | `kubectl get pvc,pv,sc`; provision PV                                        |
|                            | VolumeMount error                   | Wrong path, missing ConfigMap/Secret                         | Fix YAML mount path, check object exists                                     |
| **Control Plane**          | API Server down                     | etcd issue, cert expiry, apiserver pod crash                 | Check `/etc/kubernetes/manifests/`; verify etcd health                       |
|                            | Scheduler not scheduling            | `kube-scheduler` pod down                                    | Check scheduler logs in control plane                                        |
|                            | Controller Manager failure          | Crash/misconfig                                              | Verify controller-manager logs                                               |
| **RBAC / Security**        | `Forbidden` errors                  | Missing RBAC role/binding                                    | `kubectl auth can-i`; fix Role/ClusterRoleBinding                            |
|                            | Pod denied by policy                | PSP, OPA, Gatekeeper blocking                                | Update PodSecurityPolicy / admission policy                                  |
| **Ingress / LoadBalancer** | Ingress not routing                 | Wrong host/path, missing service, no controller              | `kubectl describe ingress`; check controller deployment                      |
|                            | External LB stuck `Pending`         | Cloud provider integration issue                             | Verify Cloud Controller Manager logs                                         |
| **Debug Tools**            | Need cluster-wide event history     | -                                                            | `kubectl get events --sort-by=.metadata.creationTimestamp`                   |
|                            | Node-level issues                   | -                                                            | `journalctl -u kubelet`                                                      |

---

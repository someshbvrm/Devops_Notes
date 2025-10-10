Here’s a clear, step-by-step guide to install **Kubernetes** (single-node using **kubeadm**) on an **Ubuntu EC2 instance**. I’ll make it suitable for AWS best practices.

---

### **1️⃣ Prerequisites**

* Ubuntu EC2 instance (Ubuntu 20.04/22.04 recommended)
* At least 2 GB RAM, 2 vCPUs
* `sudo` privileges
* Disable **swap** (required by Kubernetes)

```bash
sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab
```

---

### **2️⃣ Update system**

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y apt-transport-https ca-certificates curl gnupg
```

---

### **3️⃣ Install Docker (Container runtime)**

Kubernetes requires a container runtime like Docker.

```bash
sudo apt install -y docker.io
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker $USER
```

> Log out and log back in for Docker group changes to take effect.

---

### **4️⃣ Add Kubernetes repository**

```bash

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.34/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
sudo chmod 644 /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.34/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo chmod 644 /etc/apt/sources.list.d/kubernetes.list

sudo apt update
```

---

### **5️⃣ Install kubeadm, kubelet, and kubectl**

```bash
sudo apt install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

> Holding packages prevents accidental upgrades that break Kubernetes.

---

### **6️⃣ Initialize Kubernetes cluster**

* Use `kubeadm` to initialize a **single-node cluster**.

```bash
sudo kubeadm init --pod-network-cidr=192.168.0.0/16
```

* Note the **kubeadm join command** if you want to add worker nodes later.

---

### **7️⃣ Configure kubectl for non-root user**

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

---

### **8️⃣ Install a pod network (required for pods to communicate)**

Example: **Calico**

```bash
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
```

---

### **9️⃣ Allow scheduling pods on master (optional for single-node)**

```bash
kubectl taint nodes --all node-role.kubernetes.io/control-plane-
```

---

### **🔟 Verify installation**

```bash
kubectl get nodes
kubectl get pods --all-namespaces
```

You should see your node in `Ready` state and the system pods running.

---

✅ **Cluster ready!** You now have a single-node Kubernetes cluster on your Ubuntu EC2 instance.

---


---

# 🚀 Install Kubernetes on Ubuntu 24.04 (Step-by-Step Guide)

This guide walks you through installing a **3-node Kubernetes cluster** (1 Master + 2 Workers) on **Ubuntu 24.04** using `kubeadm`.

---

## 📌 Prerequisites

Before starting, ensure:

* 🖥️ **Ubuntu 24.04 instances** ready
* 🔑 **SSH enabled** on all nodes
* 👤 **Regular user with sudo rights**
* ⚙️ **2 CPUs, 2 GB RAM, 20 GB free disk space** (minimum per node)
* 🌐 **Stable Internet connectivity**

For demo, we use:

* **Master Node** → `k8s-master-noble (192.168.1.120)`
* **Worker Node 1** → `k8s-worker01-noble (192.168.1.121)`
* **Worker Node 2** → `k8s-worker02-noble (192.168.1.122)`

---

## 🛠️ Installation Steps

### **1️⃣ Set Hostname & Update Hosts File**

```bash
# Master Node
sudo hostnamectl set-hostname "k8s-master-noble"

# Worker Node 1
sudo hostnamectl set-hostname "k8s-worker01-noble"

# Worker Node 2
sudo hostnamectl set-hostname "k8s-worker02-noble"
```

Edit `/etc/hosts` on **all nodes**:

```
192.168.1.120  k8s-master-noble
192.168.1.121  k8s-worker01-noble
192.168.1.122  k8s-worker02-noble
```

---

### **2️⃣ Disable Swap & Load Kernel Modules**

```bash
# Disable swap
sudo swapoff -a
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab

# Load kernel modules
sudo modprobe overlay
sudo modprobe br_netfilter
```

Make it permanent:

```bash
# Add modules
sudo tee /etc/modules-load.d/k8s.conf <<EOF
overlay
br_netfilter
EOF

# Kernel parameters
sudo tee /etc/sysctl.d/kubernetes.conf <<EOT
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOT

# Apply changes
sudo sysctl --system
```

---

### **3️⃣ Install & Configure Containerd**

```bash
# Install dependencies
sudo apt install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates

# Add containerd repo
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/containerd.gpg
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

# Install containerd
sudo apt update && sudo apt install containerd.io -y

# Generate default config
containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1

# Enable systemd cgroup
sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml

# Restart service
sudo systemctl restart containerd
```

---

### **4️⃣ Add Kubernetes Repository**

```bash
# Add GPG key
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/k8s.gpg

# Add repo
echo 'deb [signed-by=/etc/apt/keyrings/k8s.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/k8s.list
```

---

### **5️⃣ Install Kubernetes Components**

```bash
sudo apt update
sudo apt install kubelet kubeadm kubectl -y
```

---

### **6️⃣ Initialize Kubernetes Cluster (Master Node only)**

```bash
sudo kubeadm init --control-plane-endpoint=k8s-master-noble
```

After init completes, configure `kubectl`:

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

You’ll get a **join command** like:

```bash
sudo kubeadm join k8s-master-noble:6443 --token <token> \
    --discovery-token-ca-cert-hash sha256:<hash>
```

---

### **7️⃣ Join Worker Nodes**

Run the **join command** on each worker node:

```bash
sudo kubeadm join k8s-master-noble:6443 --token <token> \
    --discovery-token-ca-cert-hash sha256:<hash>
```

Verify from master:

```bash
kubectl get nodes
```

---

### **8️⃣ Install Calico Network Plugin (Master Node)**

```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.29.1/manifests/calico.yaml
```

Check status:

```bash
kubectl get pods -n kube-system
kubectl get nodes
```

✅ Nodes should now be **Ready**.

---

### **9️⃣ Test Kubernetes Installation**

Create a test deployment:

```bash
kubectl create ns demo-app
kubectl create deployment nginx-app --image nginx --replicas 2 --namespace demo-app
kubectl get deployment -n demo-app
kubectl get pods -n demo-app
```

Expose service via NodePort:

```bash
kubectl expose deployment nginx-app -n demo-app --type NodePort --port 80
kubectl get svc -n demo-app
```

Test access:

```bash
curl http://<Any-Worker-IP>:<NodePort>
```

✅ You should see the **Nginx welcome page**.

---

# 🎯 Conclusion

You’ve successfully:

* 🚀 Installed Kubernetes on **Ubuntu 24.04**
* 🔗 Setup a **3-node cluster** (1 Master + 2 Workers)
* 🌐 Installed **Calico CNI**
* 📦 Deployed and exposed an **Nginx app**

Kubernetes cluster is now **up and running!** 🎉

---

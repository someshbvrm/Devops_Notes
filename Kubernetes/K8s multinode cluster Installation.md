Perfect! Let’s set up a **multi-node Kubernetes cluster on AWS EC2**. I’ll cover **VPC, subnets, security groups, EC2 instances, and Kubernetes setup** in a production-like configuration.

---

## **1️⃣ AWS Networking Setup**

### **a) Create a VPC**

* CIDR: `10.0.0.0/16`
* Enable DNS hostnames and DNS resolution.

### **b) Create subnets**

* **Public subnet** (for master node if you want public access)

  * CIDR: `10.0.1.0/24`
* **Private subnet** (for worker nodes)

  * CIDR: `10.0.2.0/24`

### **c) Internet Gateway (IGW)**

* Attach IGW to VPC for external access.

### **d) Route Tables**

* Public subnet: route `0.0.0.0/0` → IGW
* Private subnet: route `0.0.0.0/0` → NAT Gateway (for updates)

---

## **2️⃣ Security Groups**

### **a) Master Node SG**

* Allow **SSH** (port 22) from your IP
* Allow **Kubernetes API** (port 6443) from worker nodes

### **b) Worker Node SG**

* Allow **SSH** (port 22) from your IP
* Allow **Kubernetes ports** (10250, 30000-32767) from master node

---

## **3️⃣ Launch EC2 Instances**

| Node Type | Instance  | Subnet  | Role   |
| --------- | --------- | ------- | ------ |
| Master    | t3.medium | Public  | Master |
| Worker 1  | t3.medium | Private | Worker |
| Worker 2  | t3.medium | Private | Worker |

* Ubuntu 22.04 LTS
* Attach Security Groups as defined above
* Key Pair: For SSH access

---

## **4️⃣ Prepare Ubuntu EC2 Instances**

Run on **all nodes** (master & workers):

```bash
sudo apt update && sudo apt upgrade -y
sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab
sudo apt install -y apt-transport-https ca-certificates curl docker.io
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker $USER
```

---

## **5️⃣ Install Kubernetes on All Nodes**

```bash
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
sudo bash -c 'cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF'
sudo apt update
sudo apt install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

---

## **6️⃣ Initialize Master Node**

```bash
sudo kubeadm init --pod-network-cidr=192.168.0.0/16
```

* Save the **kubeadm join command**. It will look like:

```bash
kubeadm join <MASTER-IP>:6443 --token <TOKEN> --discovery-token-ca-cert-hash sha256:<HASH>
```

* Configure kubectl:

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

---

## **7️⃣ Install Pod Network**

Example: **Calico**

```bash
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
```

---

## **8️⃣ Join Worker Nodes**

On each worker node, run the **kubeadm join** command from Step 6.

---

## **9️⃣ Allow Pods on Master (Optional)**

```bash
kubectl taint nodes --all node-role.kubernetes.io/control-plane-
```

---

## **🔟 Verify Cluster**

```bash
kubectl get nodes
kubectl get pods --all-namespaces
```

* Master should be `Ready`
* Workers should be `Ready`

---

✅ **Your multi-node Kubernetes cluster is ready on AWS!**

---

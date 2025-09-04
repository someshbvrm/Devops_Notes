Here’s the **step-by-step guide** to install **Docker** and **Docker Compose** on **Ubuntu**.
This works for AWS EC2 Ubuntu instances and local Ubuntu desktops.

---

## **1. Update Your System**

```bash
sudo apt update && sudo apt upgrade -y
```

---

## **2. Uninstall Old Versions (Optional but Recommended)**

```bash
sudo apt remove docker docker-engine docker.io containerd runc
```

---

## **3. Install Required Packages**

```bash
sudo apt install apt-transport-https ca-certificates curl software-properties-common -y
```

---

## **4. Add Docker’s Official GPG Key**

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker.gpg
```

---

## **5. Add Docker Repository**

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

---

## **6. Install Docker Engine**

```bash
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io -y
```

---

## **7. Enable and Start Docker**

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

---

## **8. Run Docker Without `sudo` (Optional)**

```bash
sudo usermod -aG docker $USER
newgrp docker
```

---

## **9. Install Docker Compose**

**Latest version from GitHub:**

```bash
DOCKER_COMPOSE_VERSION=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep tag_name | cut -d '"' -f 4)
sudo curl -L "https://github.com/docker/compose/releases/download/${DOCKER_COMPOSE_VERSION}/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

---

## **10. Make It Executable**

```bash
sudo chmod +x /usr/local/bin/docker-compose
```

---

## **11. Verify Installation**

```bash
docker --version
docker-compose --version
```

---

✅ **You now have Docker + Docker Compose installed on Ubuntu.**


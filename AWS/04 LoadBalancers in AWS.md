# **Load Balancers in AWS - Theoretical Notes**

## **1. Introduction to Load Balancers**
A **Load Balancer (LB)** is a networking device or service that distributes incoming application or network traffic across multiple backend servers (targets) to ensure high availability, fault tolerance, and scalability.

### **Key Benefits:**
- **High Availability:** Distributes traffic to healthy instances.
- **Fault Tolerance:** Automatically reroutes traffic if an instance fails.
- **Scalability:** Handles increased traffic by distributing load.
- **Security:** Can integrate with AWS security features (e.g., WAF, SSL termination).

---

## **2. Types of AWS Load Balancers**
AWS offers three main types of Load Balancers:

### **a) Application Load Balancer (ALB)**
- **Layer:** **Layer 7 (HTTP/HTTPS)**
- **Use Case:** Best for web applications (microservices, container-based apps).
- **Features:**
  - Supports **path-based** and **host-based routing**.
  - Supports **WebSockets** and **HTTP/2**.
  - **Advanced request routing** (e.g., `/api` to one group, `/static` to another).
  - **Target Groups:** Can route to **EC2, ECS, Lambda, IP addresses**.
  - **SSL Termination:** Decrypts traffic at the ALB.
  - **Integration with AWS WAF** for web application security.

### **b) Network Load Balancer (NLB)**
- **Layer:** **Layer 4 (TCP/UDP)**
- **Use Case:** High-performance, low-latency applications (gaming, VoIP, real-time streaming).
- **Features:**
  - Handles **millions of requests per second** with ultra-low latency.
  - Supports **static IP addresses** and Elastic IPs.
  - Preserves **source IP address** (unlike ALB).
  - Can handle **volatile workloads** (spiky traffic).
  - **Target Groups:** EC2, IP addresses, microservices.

### **c) Gateway Load Balancer (GWLB)**
- **Layer:** **Layer 3 (IP)**
- **Use Case:** For deploying and managing **third-party virtual appliances** (firewalls, intrusion detection systems).
- **Features:**
  - Uses **GENEVE protocol** (port 6081).
  - Routes traffic to **virtual appliances** for inspection.
  - Scales **security appliances** automatically.

### **Classic Load Balancer (CLB) (Legacy)**
- **Deprecated**, but still available.
- Supports **Layer 4 (TCP/SSL)** and **Layer 7 (HTTP/HTTPS)**.
- Less flexible than ALB/NLB.

---

## **3. Key Concepts in AWS Load Balancers**

### **a) Listeners**
- A **listener** checks for connection requests (on a specified **port/protocol**).
- Example: ALB listens on **HTTPS (443)** and forwards traffic to EC2 instances.

### **b) Target Groups**
- A **logical group** of backend resources (EC2, Lambda, IPs).
- ALB/NLB route traffic to **target groups** based on rules.
- **Health Checks:** Ensures traffic only goes to healthy instances.

### **c) Rules (ALB Only)**
- Defines how traffic is routed based on **URL path, host header, query strings**.
- Example: `/images` → Target Group A, `/api` → Target Group B.

### **d) Health Checks**
- Regularly checks the health of backend instances.
- If an instance fails, traffic is rerouted to healthy ones.

### **e) Cross-Zone Load Balancing**
- Distributes traffic **evenly across all AZs** (not just the AZ with the most instances).
- **ALB:** Always enabled (no cost).
- **NLB:** Disabled by default (charges apply if enabled).

### **f) Sticky Sessions (Session Affinity)**
- Ensures a user’s session stays with the same backend instance.
- Uses **cookies** (ALB) or **source IP** (NLB).

---

## **4. Security Features**
- **SSL/TLS Termination:** Decrypts traffic at the LB (reduces backend load).
- **AWS WAF Integration:** Protects against web exploits (SQLi, XSS).
- **Security Groups:** Controls inbound/outbound traffic.
- **Private/Public Load Balancers:**
  - **Public LB:** Internet-facing.
  - **Private LB:** Internal (VPC-only) traffic.

---

## **5. Pricing Considerations**
- **ALB:** Charges based on **LCUs (Load Balancer Capacity Units)**.
- **NLB:** Charges based on **NLCUs** + data processing.
- **GWLB:** Charges based on **GLCUs** + traffic processing.
- **Cross-Zone load balancing (NLB)** incurs additional costs.

---

## **6. Choosing the Right Load Balancer**
| Feature | ALB | NLB | GWLB |
|---------|-----|-----|------|
| **Layer** | 7 (HTTP) | 4 (TCP/UDP) | 3 (IP) |
| **Use Case** | Web apps | High-performance apps | Security appliances |
| **Routing** | Path/host-based | IP-based | GENEVE-based |
| **Low Latency** | Moderate | Very High | Moderate |
| **Static IP** | No | Yes | Yes |
| **SSL Termination** | Yes | No | No |
| **Target Types** | EC2, Lambda, IP | EC2, IP | Virtual Appliances |

---

## **7. Best Practices**
- **Use ALB for HTTP/HTTPS traffic.**
- **Use NLB for extreme performance needs (TCP/UDP).**
- **Enable deletion protection** to prevent accidental deletion.
- **Monitor using CloudWatch metrics** (e.g., `RequestCount`, `HealthyHostCount`).
- **Use AWS WAF** with ALB for security.

---

### **Conclusion**
AWS Load Balancers provide scalable, highly available traffic distribution. **ALB** is best for web apps, **NLB** for low-latency needs, and **GWLB** for security appliances. Proper configuration ensures reliability, security, and cost efficiency.
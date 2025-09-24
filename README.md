# Aws-cloud-Infrastructure-Deployment

---

## ✅ **Project Title:**

**Multi-VPC Architecture with Load Balancer, Auto Scaling, and CloudFront**

---

## 🔧 **Phase 1: Network Setup**

### 1. Create VPC-A (`vpc1`)

* **CIDR:** `192.168.0.0/16`
* **Subnets:**

  * `subnet-1A` → `192.168.1.0/24` → Zone: `us-east-1a`
  * `subnet-2A` → `192.168.2.0/24` → Zone: `us-east-1b`

### 2. Create VPC-B (`vpc2`)

* **CIDR:** `10.0.0.0/16`
* **Subnets:**

  * `subnet-1B` → `10.0.1.0/24` → Zone: `us-east-1a`
  * `subnet-2B` → `10.0.2.0/24` → Zone: `us-east-1b`

---

## 🌐 **Phase 2: Internet Connectivity**

### 3. Create & Attach Internet Gateways

* IGW1 → Attach to `vpc1`
* IGW2 → Attach to `vpc2`

### 4. Route Table Updates

* `vpc1` route table → associate with subnets → add route `0.0.0.0/0` via IGW1
* `vpc2` route table → associate with subnets → add route `0.0.0.0/0` via IGW2

---

## 🔁 **Phase 3: VPC Peering**

### 5. Create VPC Peering

* Name: `vpc1to2`
* **Requester:** `vpc1`
* **Accepter:** `vpc2`
* Accept peering request manually

### 6. Update Route Tables for Peering

* `vpc1` route table → add route `10.0.0.0/16` → target: Peering connection
* `vpc2` route table → add route `192.168.0.0/16` → target: Peering connection

---

## 🔐 **Phase 4: Security Configuration**

### 7. Create Security Group for `vpc1`

* Inbound Rules:

  * SSH (22) from `0.0.0.0/0`
  * ICMP from `192.168.0.0/16` and `10.0.0.0/16`
  * HTTP (80) from `0.0.0.0/0`

### 8. Create Security Group for `vpc2`

* Inbound Rules:

  * SSH (22) from `0.0.0.0/0`
  * ICMP from `192.168.0.0/16` and `10.0.0.0/16`
  * HTTP (80) from `0.0.0.0/0`

---

## 💻 **Phase 5: EC2 Instance Deployment**

### 9. Launch Instances

* `vpc1` → Launch 1 EC2 → attach security group → enable public IP
* `vpc2` → Launch 2 EC2s → attach security group → enable public IP

### 10. Ping Test

* From `Instance 1` to `Instance 2` and vice versa (ICMP test)
* If fails, check:

  * Security group ICMP rules
  * Route tables
  * Network ACLs

---

## 🌐 **Phase 6: Web Server Setup**

### 11. Install HTTP Server

* On all EC2 instances:

  ```bash
  sudo yum install httpd -y
  sudo systemctl start httpd
  sudo systemctl enable httpd
  echo "This is Instance X" > /var/www/html/index.html
  ```

---

## 📦 **Phase 7: Load Balancing**

### 12. Create AMI Template

* From one configured instance (used in Auto Scaling)

### 13. Create Target Group

* VPC: `vpc2`
* Register EC2 instances (2 and 3)

### 14. Create Application Load Balancer (ALB)

* VPC: `vpc2`
* Subnets: All available zones
* Security group: Use one allowing HTTP
* Listener: HTTP (port 80) → forward to Target Group

### 15. Test Load Balancer

* Copy **ALB DNS name**
* Open in browser → should show alternating instance pages

---

## 📈 **Phase 8: Auto Scaling Group (ASG)**

### 16. Create Auto Scaling Group

* Use the previously created **AMI template**
* VPC: `vpc2`
* Attach to existing **security group**
* Link to:

  * **Existing ALB**
  * **Existing Target Group**

### 17. Test Auto Scaling

* Copy **ALB DNS name** again
* Open in browser → page should still load (even with scaling events)

---

## 🌍 **Phase 9: CDN Integration (CloudFront)**

### 18. Create CloudFront Distribution

* Origin Domain: **ALB DNS name**
* Settings: Keep default or optimize for caching HTTP

### 19. Test CloudFront

* Copy **CloudFront DNS URL**
* Paste in browser → app page loads through CDN

---

## 📝 **Summary Table**

| Component  | Configuration                           |
| ---------- | --------------------------------------- |
| VPCs       | vpc1: 192.168.0.0/16, vpc2: 10.0.0.0/16 |
| Subnets    | 2 per VPC, in different AZs             |
| IGWs       | 1 per VPC                               |
| EC2s       | 1 in vpc1, 2+ in vpc2                   |
| Peering    | Between vpc1 and vpc2                   |
| Security   | ICMP, SSH, HTTP allowed                 |
| ALB        | App Load Balancer in vpc2               |
| ASG        | Auto scaling in vpc2                    |
| CloudFront | Points to ALB                           |

---

## ✅ Final Checklist

* [x] VPCs and subnets created
* [x] Internet connectivity via IGW
* [x] Peering with correct routing
* [x] Security Groups configured
* [x] EC2s launched and reachable
* [x] HTTP server installed and tested
* [x] Load balancer working
* [x] Auto Scaling group configured
* [x] CloudFront distribution tested

---



# aws-ha-autoscaling-alb-vpc
AWS project demonstrating VPC, Subnets, IGW, Route Tables, Security Groups, Launch Template, Auto Scaling Group, Application Load Balancer, and CloudWatch alarms for scaling
# AWS High Availability Architecture with Auto Scaling & Load Balancer

## 📌 Project Overview
This project demonstrates how to build a **highly available web application architecture** on AWS using:
- **VPC** with custom subnets
- **Internet Gateway (IGW)** and route tables
- **Security Groups (SG)** for controlled access
- **Application Load Balancer (ALB)** for traffic distribution
- **Auto Scaling Group (ASG)** for dynamic scaling
- **CloudWatch Alarms** for monitoring and scaling actions

The setup ensures fault tolerance, scalability, and high availability.

---

## 🏗️ Architecture Components
- **VPC**: `Packup-VPC` with CIDR `10.0.0.0/16`
- **Subnets**: 3 public subnets across different AZs
- **Internet Gateway**: Attached to VPC for public access
- **Route Tables**: Configured for Internet access
- **Security Groups**:
  - `Packup-SG`: Allows SSH (22) and HTTP (80)
  - `Packup-ALB-SG`: Allows inbound HTTP (80) for ALB
- **Launch Template**: `packup-lt` with User Data to install Apache (`httpd`)
- **Auto Scaling Group**: `packup-ASG`
  - Desired capacity: 3
  - Min: 2
  - Max: 4
- **Scaling Policies**:
  - CPU > 80% → Add 1 instance
  - CPU < 30% → Remove 1 instance
- **Load Balancer**: `packup-ALB` (Internet-facing, Application type)
- **Target Group**: `packup-alb-tg` with health checks on port 80

---

## ⚙️ User Data Script
```bash
#!/bin/bash
yum update -y
yum install -y httpd
systemctl enable httpd
systemctl start httpd
echo "<h1>Instance $(curl -s http://169.254.169.254/latest/meta-data/local-ipv4)</h1>" > /var/www/html/index.html

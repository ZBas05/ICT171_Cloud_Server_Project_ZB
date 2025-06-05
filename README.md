# ICT171_Cloud_Server_Project_ZB
Cloud Server Project for ICT171
# Cloud Server Project – ICT171

**Student Name:** [Your Full Name]  
**Student Number:** [Your Student Number]  
**Live Site:** https://www.cooleatsperth.xyz  
**Video Walkthrough:** [Insert Link to Video Here]

---

## 1. Overview

This project involves developing a cloud server on WordPress running on AWS through EC2. The application of this server is to demonstrate deployment, manual setup, and fundamental infrastructure abilities through Infrastructure as a Service (IaaS).

---

## 2. EC2 Instance Setup

- **Cloud Provider:** Amazon Web Services (AWS)  
- **Service Used:** EC2 (Elastic Compute Cloud)

**Setup Details:**
- **Instance Type:** t2.micro (Free Tier)
- **Operating System:** Ubuntu 22.04 LTS
- **Key Pair Name:** ICT171ec2.pem
- **Region:** Asia Pacific (Sydney) ap-southeast-2
- **Public IPv4 Address:** [http://13.211.64.21](http://13.211.64.21)

---

## 3. Security Group Rules

A new security group was configured with the following inbound rules:

| Port | Protocol | Source      | Purpose        |
|------|----------|-------------|----------------|
| 22   | TCP      | 0.0.0.0/0   | SSH Access     |
| 80   | TCP      | 0.0.0.0/0   | HTTP Access    |
| 443  | TCP      | 0.0.0.0/0   | HTTPS Access   |

These rules ensure web traffic can reach the server and allow remote SSH configuration.

---

## 4. SSH Access

To securely access the EC2 instance, I used the following steps:

1. Adjusted file permissions on the PEM key:
   ```bash
   chmod 400 ICT171ec2.pem
2. Connected to the EC2 instance via SSH:
   ```bash
   ssh -i "ICT171ec2.pem" ubuntu@13.211.64.21
Once connected, I've got full terminal access to the instance.

---

## 5. PHP & MariaDB Installation

1. In preparation for WordPress, I installed PHP and MariaDB:
   ```bash
   sudo apt install nginx mariadb-server php-fpm php-mysql
Once configured, these packages will enable WordPress to run and interact with a backend database.

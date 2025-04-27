# 🚀 Deploying a Streamlit App on AWS EC2 with Docker

## 📌 Overview

This guide walks you through deploying a **Streamlit** application inside a **Docker** container on an **AWS EC2** instance using a custom VPC network setup. You will learn how to:

- Configure a VPC, Subnet, Route Table, and Internet Gateway
- Launch and secure an EC2 instance
- Install and configure Docker
- Transfer your application code to EC2
- Build and run your Streamlit app in Docker
- Manage your Docker container lifecycle

---

## 📖 Table of Contents

1. [Network Setup (VPC & Subnet)](#1-network-setup-vpc--subnet)
2. [Launch & Configure EC2](#2-launch--configure-ec2)
3. [Connect & Prepare EC2](#3-connect--prepare-ec2)
4. [Install Docker](#4-install-docker)
5. [Transfer Project Files](#5-transfer-project-files)
6. [Build & Run Docker Container](#6-build--run-docker-container)
7. [Access the Streamlit App](#7-access-the-streamlit-app)
8. [Manage the Container](#8-manage-the-container)
9. [Cleanup](#9-cleanup)

---

## 1️⃣ Network Setup (VPC & Subnet)

1. **Create VPC**
   - Name: `MyCustomVPC`
   - IPv4 CIDR: `10.0.0.0/16`

2. **Create Public Subnet**
   - Name: `MyPublicSubnet`
   - VPC: `MyCustomVPC`
   - CIDR: `10.0.1.0/24`
   - Enable Auto-assign Public IPv4

3. **Internet Gateway**
   - Create & attach to `MyCustomVPC`

4. **Route Table**
   - Create `MyPublicRouteTable`
   - Add route `0.0.0.0/0` → Internet Gateway
   - Associate with `MyPublicSubnet`

---

## 2️⃣ Launch & Configure EC2

1. **Launch Instance**
   - AMI: Amazon Linux 2023
   - Type: `t2.micro` (Free Tier)
   - Network: `MyCustomVPC` / `MyPublicSubnet`
   - Auto-assign Public IP: enabled
   - Security Group: allow SSH (22), HTTP (80), and Streamlit (8501)

2. **Key Pair**
   - Create or select your `.pem` key pair

---

## 3️⃣ Connect & Prepare EC2

1. **Connect via SSH**
   ```bash
   chmod 600 your-key.pem
   ssh -i your-key.pem ec2-user@<EC2_PUBLIC_IP>
   ```

2. **Update and install essentials**
   ```bash
   sudo yum update -y
   ```

---

## 4️⃣ Install Docker

```bash
sudo yum install -y docker
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker ec2-user   # (optional: run Docker without sudo)
```

---

## 5️⃣ Transfer Project Files

From your local machine:
```bash
scp -i your-key.pem app.py Dockerfile requirements.txt ec2-user@<EC2_PUBLIC_IP>:~/
```

---

## 6️⃣ Build & Run Docker Container

1. **SSH into EC2** and navigate to your project directory:
   ```bash
   cd ~
   ```

2. **Build Docker image**:
   ```bash
   docker build -t streamlit-app .
   ```

3. **Run container**:
   ```bash
   docker run -d \
     -p 8501:8501 \
     --name streamlit_app \
     streamlit-app
   ```

---

## 7️⃣ Access the Streamlit App

Open in your browser:
```
http://<EC2_PUBLIC_IP>:8501
```

---

## 8️⃣ Manage the Container

- **List running containers**: `docker ps`
- **Stop container**: `docker stop streamlit_app`
- **Remove container**: `docker rm streamlit_app`
- **Restart container**: `docker start streamlit_app`

---

## 9️⃣ Cleanup

- **Remove the container**: `docker rm -f streamlit_app`
- **Remove the image**: `docker rmi streamlit-app`
- **Leave Swarm (if used)**: `docker swarm leave --force`

---

## 🎯 Conclusion

You’ve successfully deployed a **Streamlit** application in **Docker** on an **AWS EC2** instance with a custom VPC network. You can now scale, secure, and manage your app in production!

---

🔒 *Ensure you secure your AWS credentials and follow best practices for production deployments.*


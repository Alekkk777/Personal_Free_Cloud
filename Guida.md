# 📁 Creating a Personal Cloud with Nextcloud or OwnCloud on Oracle Cloud 🚀

## 📌 Introduction
This guide will help you set up a **free, unlimited personal cloud** using **Nextcloud or OwnCloud** on an **Oracle Cloud instance**. This cloud can be used for **storage, sharing, and team collaboration**, ensuring **security and scalability**.

We will cover different installation options, network configuration, domain linking, and storage expansion.

---

## 1️⃣ Creating an Oracle Cloud Instance

### 🔹 Option 1: Manual Instance Creation
1. Log in to [Oracle Cloud](https://cloud.oracle.com/)
2. Go to **Compute** → **Instances** → **Create Instance**
3. Select the **Ubuntu 22.04** image
4. Assign **Shape VM.Standard.A1.Flex** with **1 OCPU and 6GB RAM** (scalable)
5. **Add an SSH key** for remote access
6. **Launch the instance**

### 🔹 Option 2: Quick Action (Faster & Automated)
1. **Go to Compute → Quick Actions**
2. Select **Connect Public Subnet to Internet**
3. Configure the firewall to allow **SSH (22), HTTP (80), HTTPS (443)**
4. Proceed with instance creation

---

## 2️⃣ Network Configuration & Firewall Setup

Once the instance is running, open the necessary ports in **Networking → Virtual Cloud Networks (VCN) → Security List**:

| **Port** | **Protocol** | **Usage** |
|----------|------------|----------|
| 22       | SSH        | Remote access |
| 80       | HTTP       | Unencrypted web access |
| 443      | HTTPS      | Encrypted web access |

### 🔹 Enabling UFW for Additional Security:
```bash
# Install UFW Firewall
sudo apt install ufw -y

# Allow SSH, HTTP, and HTTPS traffic
sudo ufw allow OpenSSH
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# Enable the firewall
sudo ufw enable

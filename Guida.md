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
```
---

## 3️⃣ Installing Nextcloud or OwnCloud

# Update system and install necessary packages
sudo apt update && sudo apt upgrade -y
sudo apt install apache2 mariadb-server libapache2-mod-php php php-mysql php-xml php-gd php-curl php-zip php-mbstring -y

# Download and install Nextcloud
wget https://download.nextcloud.com/server/releases/latest.tar.bz2
sudo tar -xjf latest.tar.bz2 -C /var/www/html/
sudo chown -R www-data:www-data /var/www/html/nextcloud/
sudo chmod -R 755 /var/www/html/nextcloud/

## 🔹 Option 2: Installing OwnCloud
# Download and install OwnCloud
wget https://download.owncloud.org/community/owncloud-complete-latest.tar.bz2
sudo tar -xjf owncloud-complete-latest.tar.bz2 -C /var/www/html/
sudo chown -R www-data:www-data /var/www/html/owncloud/
sudo chmod -R 755 /var/www/html/owncloud/

## 🔹 MySQL Database Configuration
# Access MySQL
sudo mysql -u root -p

# Create database and user
CREATE DATABASE nextcloud;
CREATE USER 'nextclouduser'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextclouduser'@'localhost';
FLUSH PRIVILEGES;
EXIT;

---

# 4️⃣ Domain Configuration & HTTPS
## 🌐 Option 1: DuckDNS (Free Domain)
Visit DuckDNS
Register a subdomain (e.g., yourname.duckdns.org)
Configure DNS to point to your instance’s public IP

# 🔐 Option 2: Let's Encrypt SSL for HTTPS
# Install Certbot for Apache
sudo apt install certbot python3-certbot-apache -y

# Generate and install SSL certificate
sudo certbot --apache -d yourdomain.duckdns.org

After installation, Nextcloud will be accessible securely via HTTPS.

---

# 5️⃣ Expanding Storage Capacity

If you want to add more storage (e.g., Oracle’s free 150GB block storage):
# Check available disks
sudo lsblk  

# Create a partition
sudo parted /dev/sdb mklabel gpt
sudo parted -a optimal /dev/sdb mkpart primary ext4 0% 100%

# Format the disk
sudo mkfs.ext4 /dev/sdb1

# Create a mount point and mount the disk
sudo mkdir /mnt/storage
sudo mount /dev/sdb1 /mnt/storage


Make it permanent:
# Add configuration to fstab
echo '/dev/sdb1 /mnt/storage ext4 defaults 0 2' | sudo tee -a /etc/fstab

---

# 6️⃣ Security & Server Protection

🔒 Advanced Protection with Fail2Ban

# Install Fail2Ban
sudo apt install fail2ban -y

# Enable the service
sudo systemctl enable fail2ban --now


# 🔐 Strengthening SSH Security
# Edit SSH configuration file
sudo nano /etc/ssh/sshd_config

Modify the following lines:
PermitRootLogin no
PasswordAuthentication no
MaxAuthTries 3


Restart SSH to apply changes:
# Restart SSH service
sudo systemctl restart ssh

---

# 7️⃣ Automated Backup with Cron Job
# Create a backup script
sudo nano /etc/cron.daily/backup-nextcloud

Insert the following:
#!/bin/bash
tar -czf /mnt/storage/nextcloud-backup-$(date +%F).tar.gz /var/www/html/nextcloud

Save and make executable:
# Grant execution permissions
sudo chmod +x /etc/cron.daily/backup-nextcloud

# Grant execution permissions
sudo chmod +x /etc/cron.daily/backup-nextcloud

## 📱 Mobile Access Setup
### Setting up mobile and tablet access to your Nextcloud

---

### iOS (iPhone/iPad) Setup
```bash
# 1. Download Nextcloud app
- Open App Store
- Search for "Nextcloud"
- Install official Nextcloud app

# 2. Configure connection
- Open Nextcloud app
- Enter server address:
http://your-ip/nextcloud

# 3. Login with:
- Username: your-username
- Password: your-password
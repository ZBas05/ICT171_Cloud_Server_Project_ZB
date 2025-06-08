# ICT171_Cloud_Server_Project_ZB
Cloud Server Project for ICT171
# Cloud Server Project – ICT171

**Student Name:** Zane Basso  
**Student Number:** 35130891  
**Live Site:** https://www.cooleatsperth.xyz  
**Video Explainer:** https://drive.google.com/file/d/1JdmK1obLyWvr26FhMRik78aht-Gj93Zt/view?usp=sharing


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

In preparation for WordPress, I installed PHP and MariaDB:
   ```bash
   sudo apt install nginx mariadb-server php-fpm php-mysql
   ```
Once configured, these packages will enable WordPress to run and interact with a backend database.

---

## 6. WordPress Installation and Permissions

### 6.1 Download and Extract WordPress
I downloaded the latest version of WordPress and extracted it:
```bash
sudo wget https://wordpress.org/latest.tar.gz
sudo tar -xzvf latest.tar.gz
sudo rm latest.tar.gz
```
I then made sure the extraction worked using:
```bash
ls -la
```

### 6.2 Set Ownership and Permissions
I changed ownership to allow the web server to access WordPress files:
```bash
sudo chown -R www-data:www-data wordpress/
```

I checked the file listing again:
```bash
ls -la
```

Set appropriate directory and file permissions:
```bash
sudo find wordpress/ -type d -exec chmod 755 {} \;
sudo find wordpress/ -type f -exec chmod 644 {} \;
```

Then I navigated into the WordPress directory:
```bash
cd wordpress/
ls -la
```
---

## 7. Securing MariaDB and Creating a Database

### 7.1 Secure Installation
For the secure installation, I did the following:
```bash
sudo mysql_secure_installation
```
This is how I responded to the following prompts:

- **Set root password:** ```Y```
- **Remove anonymous users:** ```Y```
- **Disallow remote root login:** ```Y```
- **Remove test database:** ```Y```
- **Reload privilege tables:** ```Y```

### 7.2 Create Database and User
To get inside the MariaDB shell, I used:
```bash
sudo mysql -u root -p
```
Once inside the MariaDB shell, I used the following commands:
```sql
CREATE DATABASE ict171_db DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
CREATE USER 'zbict171_user'@'localhost' IDENTIFIED BY 'zb_pw';
GRANT ALL PRIVILEGES ON ict171_db.* TO 'zbict171_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

---

## 8. Nginx Configuration

### 8.1 Navigate to Nginx Config Folder and Create WordPress Server Block
To get to the Nginix Config Folder, simply use the following as we will be working in the ```sites-available``` folder:
```bash
cd /etc/nginx/
ls
cd sites-available/
```
Then create the WordPress config file using:
```bash
sudo vim wordpress.conf
```
Once in the file, copy and paste the following into the file:
- Remember to edit the file, enter insert mode using ```I``` and exit it using ```ESC```.
- Once out of insert mode, use ```:wq``` to save and exit the file.

```nginx
upstream php-handler {
    server unix:/var/run/php/php8.3-fpm.sock;
}
server {
    listen 80;
    server_name cooleatsperth.xyz www.cooleatsperth.xyz;
    root /var/www/wordpress;
    index index.php;

    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass php-handler;
    }
}
```
Remember that you will need to change this part if you are not using the ```php8.3``` version.

### 8.2 Enable the Site and Test
Use the commands below to:
- Check that the PHP socket exists:
```bash
ls /var/run/php/php8.3-fpm.sock
```

- Then link the config file:
```bash
sudo ln -s /etc/nginx/sites-available/wordpress.conf /etc/nginx/sites-enabled/
```

- Test the Nginx config:
```bash
sudo nginx -t
```

- Restart Nginx:
```bash
sudo systemctl restart nginx
```

---

## 9. Installing Additional PHP Extensions
WordPress requires several common PHP modules that do not come pre-installed:
```bash
sudo apt install php-curl php-dom php-mbstring php-imagick php-zip php-gd php-intl
```
These were the one that I needed, however you may need more or less depending on what your Wordpress says.

---

## 10. Install and Configure SSL with Certbot
### 10.1 Install Certbot
I installed Certbot using the following commands bellow:
```bash
sudo apt install snapd
sudo snap install core; snap refresh core
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```
You will need to wait a bit for it to download.

### 10.2 Obtain SSL Certificate
After it is done downloading, to get the SSL Certificate use the command bellow:
```bash
sudo certbot --nginx
```

Certbot will prompt you to select the domain(s) to enable HTTPS, check that they're the correct domains and choose both of them. It will automatically configure Nginx to redirect HTTP to HTTPS.
After this go back to your Wordpress dashboard and change the WordPress Address and Site Address form "http" to "https".

---

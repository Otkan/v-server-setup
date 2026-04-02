# V-Server Setup Documentation

## Overview

This repository documents the setup and configuration of a Linux-based V-Server, including SSH access, web server installation, domain configuration, HTTPS setup, and Git integration.

---

## 1. SSH Key Setup

An SSH key was added to enable secure login to the server without using a password.

### Steps

* Generated an SSH key pair on the local machine:

  ```bash
  ssh-keygen -t ed25519
  ```
* Copied the public key to the server:

  ```bash
  ssh-copy-id user@server-ip
  ```
* Verified SSH login:

  ```bash
  ssh user@server-ip
  ```

---

## 2. Disable Password Authentication

After confirming that SSH login via key works, password authentication was disabled.

### Steps

* Opened the SSH configuration:

  ```bash
  sudo nano /etc/ssh/sshd_config
  ```
* Changed the following setting from #PasswordAuthentication no to:

  ```
  PasswordAuthentication no
  ```
* Restarted the SSH service:

  ```bash
  sudo systemctl restart ssh
  ```

---

## 3. Install NGINX

NGINX was installed as the web server.

### Installation

```bash
sudo apt update
sudo apt install nginx -y
```

### Verification

* Entered the server IP address in the browser
* Confirmed that the default NGINX welcome page was shown

---

## 4. Custom NGINX Configuration

Instead of modifying the default page, a separate directory and custom NGINX configuration were created.

### Create web directory

```bash
sudo mkdir -p /var/www/alternatives
```

### Create custom HTML file

```bash
sudo nano /var/www/alternatives/alternate-index.html
```

### Create custom NGINX site configuration

```bash
sudo nano /etc/nginx/sites-enabled/alternatives
```

### Configuration

```nginx
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    server_name deine-domain.de www.deine-domain.de;

    root /var/www/alternatives;
    index alternate-index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

---

## 5. Remove Default NGINX Page

The default configuration was removed to prevent the standard NGINX page from being served.

```bash
sudo rm /etc/nginx/sites-enabled/default
```

---

## 6. Validate and Reload NGINX

```bash
sudo nginx -t
sudo systemctl reload nginx
```

---

## 7. Domain Configuration

A domain was configured to point to the server using DNS.

### Steps

* Opened the domain settings in the hosting provider (e.g. STRATO)
* Created an A record:

```
Type: A
Name: @
Value: <your-server-ip>
```

Optional:

```
Type: A
Name: www
Value: <your-server-ip>
```

### Verification

```bash
ping deine-domain.de
```

The domain should resolve to the server IP address.

---

## 8. Install Certbot

Certbot was installed to enable HTTPS using Let's Encrypt.

```bash
sudo apt update
sudo apt install certbot python3-certbot-nginx -y
```

---

## 9. Enable HTTPS with Certbot

Certbot was used to automatically generate and configure SSL certificates.

```bash
sudo certbot --nginx
```

### During setup

* Entered email address
* Accepted terms
* Selected the domain
* Enabled automatic HTTP → HTTPS redirect

---

## 10. Git Installation and Configuration

Git was installed

```bash
sudo apt update
sudo apt install git -y
```

Git was configured with user credentials.

```bash
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
```

---

## 11. GitHub SSH Integration (Server Side)

An SSH key was generated on the server to allow interaction with GitHub repositories.

### Steps

```bash
ssh-keygen -t ed25519 -C "server-github"
cat ~/.ssh/id_ed25519.pub
```

The public key was added to GitHub.

### Test connection

```bash
ssh -T git@github.com
```

Expected output:

```
Hi USERNAME! You've successfully authenticated, but GitHub does not provide shell access.
```

---

## 12. Clone Repository on Server

The Git repository was cloned to the server using SSH authentication.

```bash
git clone git@github.com:USERNAME/REPOSITORY.git
```

---

## 13. Final Verification

* Server is reachable via domain
* HTTPS is active (valid SSL certificate)
* HTTP is redirected to HTTPS
* Custom NGINX page is displayed
* Default NGINX page is removed
* SSH login works with key authentication only
* GitHub access via SSH works

---

## Notes

* No sensitive data (passwords, private keys) is stored in this repository
* Configuration is reproducible and documented step by step

---

## Server Information

* Server IP: `<your-server-ip>`
* Domain: `<your-domain>`
* OS: Linux (e.g. Ubuntu)
* Web Server: NGINX
* SSL: Let's Encrypt (Certbot)
* Version Control: Git + GitHub

---

## Conclusion

The V-Server has been successfully configured with secure SSH access, a custom NGINX setup, domain integration, HTTPS encryption, and GitHub integration. The setup follows best practices for security and maintainability.

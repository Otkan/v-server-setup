# V-Server Setup Documentation

This repository documents the setup and configuration of a Linux-based V-Server, including SSH access, web server installation, and Git integration.

---

## Table of Contents

* [1. SSH Key Setup](#1-ssh-key-setup)
* [2. Disable Password Authentication](#2-disable-password-authentication)
* [3. Install NGINX](#3-install-nginx)
* [4. Custom NGINX Configuration](#4-custom-nginx-configuration)
* [5. Remove Default NGINX Page](#5-remove-default-nginx-page)
* [6. Validate and Reload NGINX](#6-validate-and-reload-nginx)
* [7. Git Installation and Configuration](#7-git-installation-and-configuration)
* [8. GitHub SSH Integration](#8-github-ssh-integration-server-side)
* [9. Clone Repository](#9-clone-repository-on-server)
* [10. Final Verification](#10-final-verification)

---

## 1. SSH Key Setup

An SSH key is used to enable secure login to the server without using a password.

### Steps

Generate an SSH key pair on the local machine:

```bash
ssh-keygen -t ed25519
```

Copy the public key to the server:

```bash
ssh-copy-id <user>@<server-ip>
```

⚠️ If multiple SSH keys exist, specify the correct one:

```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub <user>@<server-ip>
```

Verify SSH login:

```bash
ssh <user>@<server-ip>
```

---

## 2. Disable Password Authentication

After confirming that SSH login via key works, password authentication is disabled.

### Steps

Open the SSH configuration:

```bash
sudo nano /etc/ssh/sshd_config
```

Ensure the following line is set correctly:

```text
PasswordAuthentication no
```

⚠️ If the line is commented (`#PasswordAuthentication no`), remove the `#`.

Restart the SSH service:

```bash
sudo systemctl restart ssh
```

---

## 3. Install NGINX

NGINX is installed as the web server.

### Installation

```bash
sudo apt update
sudo apt install nginx -y
```

### Verification

* Open `<server-ip>:8081` in the browser
* Confirm that the NGINX page is displayed

---

## 4. Custom NGINX Configuration

A custom HTML page is configured as the entry point.

### Create web directory

```bash
sudo mkdir -p /var/www/alternatives
```

### Create custom HTML file

```bash
sudo nano /var/www/alternatives/alternate-index.html
```

### Create NGINX configuration

```bash
sudo nano /etc/nginx/sites-enabled/alternatives
```

### Configuration

```nginx
server {
    listen 8081;
    listen [::]:8081;

    server_name <server-ip>;

    root /var/www/alternatives;
    index alternate-index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

---

## 5. Remove Default NGINX Page

Remove the default configuration:

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

## 7. Git Installation and Configuration

Install and configure Git:

```bash
sudo apt update
sudo apt install git -y
```

```bash
git config --global user.name "<your-name>"
git config --global user.email "<your-email>"
```

---

## 8. GitHub SSH Integration (Server Side)

Generate an SSH key on the server:

```bash
ssh-keygen -t ed25519 -C "server-github"
cat ~/.ssh/id_ed25519.pub
```

Add the public key to GitHub.

### Test connection

```bash
ssh -T git@github.com
```

---

## 9. Clone Repository on Server

```bash
git clone git@github.com:<username>/<repository>.git
```

---

## 10. Final Verification

* SSH login works with key authentication
* Password login is disabled
* Web server is reachable via `<server-ip>:8081`
* Custom HTML page is displayed
* GitHub access via SSH works

---

## Notes

* No sensitive data (passwords, private keys) is stored in this repository
* The setup is reproducible and documented step by step

---

## Server Information

* Server IP: `<server-ip>`
* OS: Linux (e.g. Ubuntu)
* Web Server: NGINX
* Version Control: Git + GitHub

---

## Conclusion

The V-Server is configured with secure SSH access, a custom NGINX setup, and GitHub integration. The setup follows best practices for security and maintainability.

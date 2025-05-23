**DevOps project** that demonstrates CI/CD using a Node.js application deployed on an **Amazon Linux EC2 instance**, with GitHub integration, **NGINX as a reverse proxy**, **PM2 for process management**, and **Git hooks for automated deployment**.

---

## 🔧 Project: Node.js Web App Deployment on Amazon Linux (EC2) with GitHub Integration

### 💡 Project Overview

* Deploy a Node.js app on Amazon Linux EC2
* Use GitHub for source control
* Automatically pull changes on `git push`
* Use NGINX as a reverse proxy
* Use PM2 to keep the app running

---

## 🔨 Tech Stack

* **Amazon Linux 2023**
* **Node.js**
* **PM2**
* **NGINX**
* **GitHub**
* **Git Webhooks** or `post-receive` Git hook

---

## 🧾 Folder Structure on EC2

```
/var/www/app/
├── .git/
├── app.js
├── ecosystem.config.js
└── package.json
```

---

## 1️⃣ EC2 Setup & SSH

```bash
# Launch EC2 with Amazon Linux 2023
# SSH into instance
ssh -i your-key.pem ec2-user@your-ec2-public-ip

# Update system
sudo yum update -y
```

---

## 2️⃣ Install Node.js, NPM, Git, and PM2

```bash
# Install Node.js and Git
sudo dnf module enable nodejs:18 -y
sudo dnf install nodejs git -y

# Install PM2 globally
sudo npm install pm2 -g
```

---

## 3️⃣ Clone Your GitHub Repo

```bash
# Replace with your own repo
cd /var/www
sudo git clone https://github.com/yourusername/your-node-app.git app
cd app

# Install dependencies
npm install
```

---

## 4️⃣ Set Up PM2 Process

```bash
# Start app with PM2
pm2 start app.js --name nodeapp

# Save PM2 process
pm2 save

# Auto-start on reboot
pm2 startup
# Follow the output instruction (e.g., sudo env PATH=... pm2 startup ...)
```

---

## 5️⃣ Install and Configure NGINX

```bash
sudo amazon-linux-extras enable nginx1
sudo yum install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
```

### 🔧 NGINX Config

```bash
sudo nano /etc/nginx/conf.d/nodeapp.conf
```

```nginx
server {
    listen 80;
    server_name your-domain.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

```bash
sudo nginx -t
sudo systemctl reload nginx
```

---

## 6️⃣ Git Pull Script via Hook (Optional for Auto Deployment)

### Create a Bare Repo and Setup Post-Receive Hook

```bash
mkdir /home/ec2-user/repos
cd /home/ec2-user/repos
git init --bare nodeapp.git
```

### Hook Setup

```bash
cd nodeapp.git/hooks
nano post-receive
```

```bash
#!/bin/bash
GIT_WORK_TREE=/var/www/app git checkout -f
cd /var/www/app
npm install
pm2 restart nodeapp
```

```bash
chmod +x post-receive
```

---

## 7️⃣ Push Code from Local to EC2 (Once SSH Key is Added)

```bash
git remote add production ec2-user@your-ec2-ip:/home/ec2-user/repos/nodeapp.git
git push production master
```

---

## ✅ Optional: Configure Firewall

```bash
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --reload
```

---

## 🎯 Final Outcome

* App runs on `http://<your-ec2-public-ip>`
* Code can be updated via `git push`
* PM2 ensures uptime
* NGINX routes HTTP traffic to Node.js

---

Would you like a downloadable ZIP or GitHub repo for this project with a sample `app.js` and `package.json` included?

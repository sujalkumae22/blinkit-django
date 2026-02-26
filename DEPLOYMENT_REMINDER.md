cat <<EOF > /home/ubuntu/quick-commerce-django/DEPLOYMENT.md

# 🚀 Django Hosting & Deployment Guide

This document provides a professional step-by-step guide for hosting the **Quick-Commerce Django** project on **Ubuntu/EC2** using **Gunicorn**, **Nginx**, and **AWS RDS**.

---

## 1. Environment & Code Setup

- **Clone the Repository:** \`git clone <repo_url>\` and enter the directory.
- **Create Virtual Environment:** \`python3 -m venv venv\`.
- **Install Dependencies:** \`source venv/bin/activate\` followed by \`pip install -r requirements.txt\`.
- **Configure Environment Variables:** Create a \`.env\` file in the root directory.
  - _Required:_ \`SECRET_KEY\`, \`DB_NAME\`, \`DB_USER\`, \`DB_PASSWORD\`, \`DB_HOST\`, and \`EMAIL\` settings.

## 2. Database & Assets

- **Install MySQL Headers:** \`sudo apt install python3-dev default-libmysqlclient-dev build-essential -y\` and \`pip install mysqlclient\`.
- **SSL Certificate:** Download the AWS RDS certificate bundle to a \`certs/\` folder to secure the connection.
- **Migrate Database:** \`python manage.py migrate\`.
- **Seed Data:** Run \`python create_categories.py\` to populate the store categories.
- **Collect Static Files:** \`python manage.py collectstatic --noinput\`.

## 3. Application Server (Gunicorn)

- **Install Gunicorn:** \`pip install gunicorn\`.
- **Create Systemd Service:** Create \`/etc/systemd/system/gunicorn.service\`.
  - **User:** ubuntu
  - **ExecStart:** Points to \`/home/ubuntu/quick-commerce-django/venv/bin/gunicorn\`.
  - **Socket:** Binds to \`unix:/home/ubuntu/quick-commerce-django/gunicorn.sock\`.
- **Enable Service:** \`sudo systemctl start gunicorn\` and \`sudo systemctl enable gunicorn\`.

## 4. Web Server (Nginx)

- **Install Nginx:** \`sudo apt install nginx -y\`.
- **Configure Server Block:** Create \`/etc/nginx/sites-available/quick-commerce\`.
  - Set \`server_name\` to your Public IP (e.g., \`13.60.197.90\`).
  - Add **Static** and **Media** location blocks.
- **Enable Config:** Link to \`sites-enabled\`, remove the default config, and run \`sudo systemctl restart nginx\`.

## 5. Security & Permissions

- **AWS Security Group Rules:**
  - **Port 80 (HTTP):** Allow from \`0.0.0.0/0\` for web traffic.
  - **Port 3306 (MySQL):** Allow traffic between EC2 and RDS.
- **Folder Permissions:** Grant Nginx (\`www-data\`) permission to read the project.
  - \`sudo chmod 755 /home/ubuntu\`.
  - \`sudo chown -R ubuntu:www-data /home/ubuntu/quick-commerce-django/media\`.

## 6. Monitoring & Maintenance

- **Django/Python Logs:** \`sudo journalctl -u gunicorn -f\`.
- **Nginx Error Logs:** \`sudo tail -f /var/log/nginx/error.log\`.
- **Service Status:** \`sudo systemctl status gunicorn nginx\`.

---


_Created on: $(date)_
EOF


# 🚀 Server Stop & Start Guide (AWS EC2 / Nginx / Docker / Django)

This document contains useful commands to **start, stop, and restart** services on an Ubuntu EC2 server.

---

# 1️⃣ EC2 Instance Control (Complete Server)

## Stop EC2 Instance

From AWS Console:

```
AWS → EC2 → Instances → Select Instance → Instance State → Stop
```

From SSH:

```bash
sudo shutdown now
```

---

## Start EC2 Instance

```
AWS → EC2 → Instances → Select Instance → Instance State → Start
```

⚠️ Public IP may change if Elastic IP is not attached.

---

# 2️⃣ Nginx Web Server

## Stop Nginx

```bash
sudo systemctl stop nginx
```

## Start Nginx

```bash
sudo systemctl start nginx
```

## Restart Nginx

```bash
sudo systemctl restart nginx
```

## Check Status

```bash
sudo systemctl status nginx
```

---

# 3️⃣ Django / Gunicorn Service

## Stop Gunicorn

```bash
sudo systemctl stop gunicorn
```

## Start Gunicorn

```bash
sudo systemctl start gunicorn
```

## Restart Gunicorn

```bash
sudo systemctl restart gunicorn
```

---

# 4️⃣ Docker Containers

## List Running Containers

```bash
docker ps
```

## Stop Container

```bash
docker stop <container_name>
```

## Start Container

```bash
docker start <container_name>
```

## Restart Container

```bash
docker restart <container_name>
```

## Stop All Containers

```bash
docker stop $(docker ps -q)
```

---

# 5️⃣ PM2 (Node.js Apps)

## Stop All Apps

```bash
pm2 stop all
```

## Start All Apps

```bash
pm2 start all
```

## Restart All Apps

```bash
pm2 restart all
```

---

# 6️⃣ Firewall (Optional)

## Check UFW Status

```bash
sudo ufw status
```

## Restart Firewall

```bash
sudo systemctl restart ufw
```

---

# 📌 Common Quick Commands

Restart full web stack:

```bash
sudo systemctl restart nginx
sudo systemctl restart gunicorn
```

Docker stack restart:

```bash
docker restart $(docker ps -q)
```

---

# ✅ Notes

* Always test config before restarting Nginx:

```bash
sudo nginx -t
```

* Use Elastic IP to avoid IP change.
* Keep port 22 restricted for security.

---

**Author:** Chandan Yadav
**Project:** EC2 Deployment Notes


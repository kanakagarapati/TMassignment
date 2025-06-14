# TravelMemory Deployment - AWS EC2 (Frontend + Backend + Load Balancers)

This project documents the complete deployment of the **TravelMemory** MERN stack application on AWS EC2 with load balancers, custom domain configuration via Cloudflare, and HTTPS enabled using Certbot.

---

## 📌 Architecture Overview

The application is deployed using:

- **2 Frontend EC2 Instances**
- **2 Backend EC2 Instances**
- **Application Load Balancer** (for frontend)
- **Application Load Balancer** (for backend)
- **Cloudflare DNS** for custom domain pointing
- **Nginx** as a reverse proxy on all instances
- **Certbot** for HTTPS (Let's Encrypt)
## Architecture Diagram
<img width="455" alt="image" src="https://github.com/user-attachments/assets/7527ced5-21d4-4652-898b-abb404632ecc" />

---

## ⚙️ Backend Deployment Steps

1. **Launch EC2 Instance**  
   - Name: `KanakaManoj-TM-BE-WUS2-01`
   - OS: Ubuntu 24.04

2. **Install Dependencies**
   ```bash
   sudo apt update
   sudo apt install nodejs npm nginx -y
   ```

3. **Clone Repository**
   ```bash
   git clone https://github.com/UnpredictablePrashant/TravelMemory.git
   cd TravelMemory/backend
   ```

4. **Set Up Environment File**
   ```bash
   sudo nano .env
   ```

5. **Install Node Packages & Start**
   ```bash
   npm install
   pm2 start index.js --name travel-backend
   ```

6. **Configure Nginx Reverse Proxy**
   ```nginx
   server {
       listen 80;
       server_name api.kmgtm.info;

       location / {
           proxy_pass http://localhost:3000;
           proxy_http_version 1.1;
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_set_header Upgrade $http_upgrade;
           proxy_set_header Connection 'upgrade';
       }
   }
   ```

7. **Enable HTTPS with Certbot**
   ```bash
   sudo apt install certbot python3-certbot-nginx -y
   sudo certbot --nginx -d api.kmgtm.info
   ```

---

## 🎨 Frontend Deployment Steps

1. **Launch EC2 Instance**  
   - Name: `KanakaManoj-TM-FE-WUS2-01`

2. **Clone and Build Frontend**
   ```bash
   git clone https://github.com/UnpredictablePrashant/TravelMemory.git
   cd TravelMemory/frontend
   npm install
   npm run build
   ```

3. **Serve React Build**
   ```bash
   sudo cp -r build/* /var/www/html/
   sudo nano /etc/nginx/sites-available/default
   sudo systemctl restart nginx
   ```

4. **Enable HTTPS with Certbot**
   ```bash
   sudo apt install certbot python3-certbot-nginx -y
   sudo certbot --nginx -d <frontend-domain>
   ```

---

## ☁️ Load Balancers & Target Groups

1. **Create Target Groups**
   - `TG-FE` for Frontend EC2s
   - `TG-BE` for Backend EC2s

2. **Create Load Balancers**
   - `ALB-FE` pointing to `TG-FE`
   - `ALB-BE` pointing to `TG-BE`

3. **Configure Security Groups**
   - Allow HTTP (80), HTTPS (443), and custom ports if needed

---

## 🌐 Domain Configuration

1. **Purchase Domain**
   - From GoDaddy

2. **Configure DNS on Cloudflare**
   - Point frontend and backend subdomains (e.g., `api.kmgtm.info`) to respective Load Balancer DNS
   - Enable SSL in Cloudflare

---

## ✅ Health Check & Testing

- Ensure EC2 instances pass health checks in their target groups
- Backend running: `https://api.kmgtm.info`
- Frontend running: `https://kmgtm.info`

---

## 🔒 SSL Details

- Issued via Let's Encrypt
- Auto-renewal configured using Certbot
- Cert location:
  ```
  /etc/letsencrypt/live/<domain>/fullchain.pem
  ```

---

## 🧑‍💻 Author

**Kanaka Manoj Garapati**

---

## 📎 Code taken from below Repository

[GitHub - TravelMemory](https://github.com/UnpredictablePrashant/TravelMemory)

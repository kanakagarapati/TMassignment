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
   - Back End
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
    }
}

server {
    listen 443 ssl;
    server_name api.kmgtm.info;

    ssl_certificate /etc/letsencrypt/live/api.kmgtm.info/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/api.kmgtm.info/privkey.pem;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
   ```
![image](https://github.com/user-attachments/assets/051b286e-33bb-4964-bfca-a445d088b307)

</br>
   - Front end 
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

4. **Enabled HTTPS with Certbot**
   ```bash
   sudo apt install certbot python3-certbot-nginx -y
   sudo certbot --nginx -d <frontend-domain>
   ```

---

## ☁️ Load Balancers & Target Groups

1. **Created Target Groups**
   - `kanakaManoj-FE-target-group` for Frontend EC2s
   - `kanakaManoj-BE-target-group` for Backend EC2s

2. **Created Load Balancers**
   - `KanakaManoj-FE-LB` pointing to `kanakaManoj-FE-target-group`
   - `KanakaManoj-BE-LB` pointing to `kanakaManoj-BE-target-group`

3. **Configure Security Groups**
   - Allow HTTP (80), HTTPS (443), and custom ports if needed

---

## 🌐 Domain Configuration

1. **Purchase Domain**
   - From GoDaddy

2. **Configure DNS on Cloudflare**
   - Point frontend and backend subdomains (`api.kmgtm.info`) to respective Load Balancer DNS
   - Enable SSL in Cloudflare </br>
   **Before configuring Load balancer**</br>
   <p>
   <img width="452" alt="image" src="https://github.com/user-attachments/assets/92a99805-637c-43d7-a781-8eebee05f0db" /></p></br>

   **After configuring Load balancer**</br>
   <p>
   <img width="452" alt="image" src="https://github.com/user-attachments/assets/1beec478-364f-4fdc-bb7e-1e282f96f3a0" /></p></br>


---

## ✅ Health Check & Testing

- Ensureing my EC2 instances pass health checks in their target groups
- Backend running: `https://api.kmgtm.info`
- Frontend running: `https://kmgtm.info` </br>
  **Below are the screens for app health and application testing with custom domain and LB.**
  - App Load</br>
  <p>
  <img width="452" alt="image" src="https://github.com/user-attachments/assets/d212509f-bd44-4cf1-b9f3-cc37ff4457fe" /></br>
  <img width="452" alt="image" src="https://github.com/user-attachments/assets/5c311c02-4268-4327-a22b-cfa5525a1199" /></p>
  - DB screens
  <p><img width="452" alt="image" src="https://github.com/user-attachments/assets/b653f6ff-f348-4aa1-b6a0-683df423cb37" /></p>

  - App health screens</br>
  
   <p><img width="452" alt="image" src="https://github.com/user-attachments/assets/64a91a7b-3055-45ed-b3ea-93a1e6c361ed" /></p></br>
    <p><img width="432" alt="image" src="https://github.com/user-attachments/assets/d9ac5bc6-67fa-495c-a539-bf6d06b0cbea" /></p></br>






---

## 🔒 SSL Details

- Issued via Let's Encrypt
- Auto-renewal configured using Certbot
- Cert location:
  ```
  /etc/letsencrypt/live/<domain>/fullchain.pem
  ```
## Complete assignment documentation link
**Click on <a href="https://docs.google.com/document/d/1GurgdBSHPK7NMF84HVahdybzLc8uT_FX/edit?usp=sharing&ouid=109749314648122414873&rtpof=true&sd=true">Complete Assignment Documentation</a> like to see all the detailed steps and test results and app health**
---

## 🧑‍💻 Author

**Kanaka Manoj Garapati**

---

## 📎 Code taken from below Repository

[GitHub - TravelMemory](https://github.com/UnpredictablePrashant/TravelMemory)

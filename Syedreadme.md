
# TravelMemory — End-to-End Deployment Guide (Amazon Linux EC2 + nginx + ALB + Cloudflare)


## Prerequisites
- AWS account with permissions for EC2, ALB, Auto Scaling, ACM.
- A MongoDB database (Atlas recommended) or self-managed MongoDB.
- A domain (Cloudflare-managed DNS recommended).
- SSH access to EC2 (key pair).
- GitHub repo: `TravelMemory` (frontend + backend).

---

## 1) Launch EC2 (Amazon Linux 2)
1. **Create Security Group** (SG):
   - Inbound: HTTP (80) from `0.0.0.0/0`, HTTPS (443) if using TLS on instance; SSH (22) from **your IP only**.
   - Outbound: Allow all (default).

2. **Launch EC2**:
   - AMI: Amazon Linux 2.
   - Instance type: t3.micro (demo) / t3.small+ (prod).
   - Attach SG above.
   - Storage: 20 GB gp3 recommended.
   - Key pair: select or create.
   - **User data** (optional bootstrap): (see Appendix A).

<img width="1792" height="760" alt="image" src="https://github.com/user-attachments/assets/6798bf2c-db40-45de-b3af-f3f2f2ac45d8" />


---

## 2) Install dependencies on EC2
SSH to the instance, then run:

    ```bash
    sudo yum update -y
    sudo yum install -y git nginx
    
    # Node.js 18 LTS
    sudo yum install -y nodejs
    
    # PM2 for process management
    sudo npm install -g pm2@5
    
    # Enable nginx
    sudo systemctl enable nginx
    sudo systemctl start nginx



<img width="543" height="201" alt="image" src="https://github.com/user-attachments/assets/2c2ccd25-f8b7-4ca0-a4d5-54aff355256e" />.

---

## 3) Clone repo and install app

    git clone https://github.com/imran7012/TravelMemory.git .
    
    # Backend
    cd backend
    npm ci || npm install
    
    # Frontend
    cd ../frontend
    npm ci || npm install


<img width="832" height="321" alt="image" src="https://github.com/user-attachments/assets/ae383689-3c2f-4117-8296-fd60a41db5a2" />


---

## 4) Configure backend
Create `backend/.env`:
  
PORT=3001
MONGODB_URI=DB connection string

<img width="1895" height="230" alt="image" src="https://github.com/user-attachments/assets/9109ec2d-ca2e-4f59-90f6-7d7ae095bc3e" />

---


## 5) Build frontend & run backend with PM2
  
  # Start backend
  cd ../backend
  pm2 start index.js --name travelmemory-backend
  pm2 save
  pm2 startup systemd
  pm2 ls (lisys of process-Manager)
  

<img width="1852" height="213" alt="image" src="https://github.com/user-attachments/assets/40286203-dba8-4ecb-8a97-f857fa447619" />


---

## 6) Configure nginx (serve React + proxy `/api` → backend:3001)
Create `/etc/nginx/conf.d/travelmemory.conf`:

```nginx

server {
listen 80;
server_name ALB-Travel-Memory-1898338712.ap-south-1.elb.amazonaws.com; # replace


root /var/www/frontend;
index index.html index.htm;


location /static/ {
# static assets
try_files $uri $uri/ =404;
}


location /trip/ {
proxy_pass http://127.0.0.1:3001;
proxy_http_version 1.1;
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection 'upgrade';
proxy_set_header Host $host;
proxy_cache_bypass $http_upgrade;
}


# for SPA client-side routing, return index.html for other requests
location / {
try_files $uri /index.html;
}
}

```

If you hit `server_names_hash_bucket_size` error, add this inside `http {}` in `/etc/nginx/nginx.conf`:
```
server_names_hash_bucket_size 128;
```

Test & reload:

sudo nginx -t
sudo systemctl reload nginx

<img width="841" height="128" alt="image" src="https://github.com/user-attachments/assets/5a4d8beb-548c-4bef-9801-f36fb60d18ce" />


---

## 7) Frontend ↔ Backend connection'
   cd /frontend
   create .env
   npm install
   npm run build
   once the build folder is created, please copy build folder to /var/www/frontend/
  
   cp -rvf ./build/* /var/www/frontend/

<img width="1110" height="125" alt="image" src="https://github.com/user-attachments/assets/645f0095-e48c-455b-9940-86faf3acaa75" />


---

## 8) Create ALB + Auto Scaling Group (Scaling)
  **High-level**: ALB → Target Group (EC2 instances running nginx on port 80) → Auto Scaling Group across 2+ AZs.
  
  Steps:
  1. **Create Launch Template** using your configured instance (or bake an AMI).
     - User Data (bootstraps app): see **Appendix A**.
  2. **Create Target Group** (Instances, HTTP 80); Health check path: `/api/health`.
  3. **Create ALB** (Internet-facing) with listener 80 (and 443 if using TLS).
  4. **Create ASG** using the Launch Template.
     - Min: 2, Desired: 2, Max: (your choice).
     - Attach the Target Group.
  5. **Security Groups**:
     - ALB SG: allow inbound 80/443 from `0.0.0.0/0`.
     - EC2 SG: allow inbound 80 **from ALB SG only**, SSH from your IP.
  6. **Test** ALB DNS name in browser.

<img width="1905" height="742" alt="image" src="https://github.com/user-attachments/assets/67754a9c-fc9b-4bf6-8567-e953e053441e" />


<img width="1892" height="806" alt="image" src="https://github.com/user-attachments/assets/9bcf5260-b197-4525-a07a-246bb46c39e7" />

Auto-scaling instances are increasing depending on CPU utilization

<img width="1885" height="422" alt="image" src="https://github.com/user-attachments/assets/1e91f11b-7f8e-487d-92be-b2398c0b3ca3" />


---

## 9) Cloudflare DNS & TLS
I was unable to create cloudflare DNS, I used ALB DNS for accessing the TravelMemory application


---

## 10) Verification of Apllication

<img width="1905" height="1028" alt="image" src="https://github.com/user-attachments/assets/c33bd973-75d0-4b17-b1c0-6bc1a6579804" />


---


## 13) Architecture Diagram

<img width="1208" height="278" alt="image" src="https://github.com/user-attachments/assets/f46ab8e4-5e58-4fc1-bcc8-450233026fab" />

----


# VPS - Deploying Multiple Node.js Apps on a VPS with Nginx, PM2, and SSL

## 1\. Connect to Your VPS via SSH

```bash
ssh root@your_server_ip

Ajibolatao1.
```

---

## 2\. Update Your VPS

```bash
sudo apt update && sudo apt upgrade -y
```

### Create a New User (recommended instead of using root):

```bash
sudo adduser newuser
```

```bash
sudo usermod -aG sudo newuser
```

```bash
su - newuser
```

```bash
sudo whoami
```

---

## 3\. Install Node.js, NPM, and Git

```bash
curl -fsSL https://deb.nodesource.com/setup_22.x -o nodesource_setup.sh
```

```bash
sudo -E bash nodesource_setup.sh
```

```bash
sudo apt install -y nodejs
```

```bash
sudo apt install git -y
```

**Verify Installation:**

```bash
node -v
```

```bash
npm -v
```

```bash
git --version
```

---

## 4\. Install PM2 (Process Manager)

```bash
sudo npm install -g pm2
```

**Verify Installation:**

```bash
pm2 -v
```

---

## 5\. Install and Configure Nginx

```bash
sudo apt install nginx
```

**Start & Enable Nginx:**

```bash
sudo systemctl start nginx
```

```bash
sudo systemctl enable nginx
```

```bash
sudo systemctl status nginx
```

**Allow Access to Required Ports:**

```bash
sudo ufw allow OpenSSH
```

```bash
sudo ufw allow 'Nginx HTTP'
```

```bash
sudo ufw allow 'Nginx Full'
```

---

```bash
sudo ufw allow 8080
```

## 6\. Setup SSH Keys for GitHub (Private Repo Access)

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

```bash
cat ~/.ssh/id_ed25519.pub
```

- (Add the key at GitHub → Settings → SSH Keys) ([here](https://github.com/settings/keys))

```bash
git clone git@github.com:your-username/your-private-repo.git
```

---

## 7\. Clone Your App

```bash
git clone your_repository_url /path/to/your/app
```

```bash
git pull
```

**Navigate & Install Dependencies:**

```bash
cd /path/to/your/app
```

```bash
npm install
```

---

## 8\. Run Apps with PM2

Navigate into app folder, then run:

```bash
pm2 start npm --name *appName* --watch -- start

# e.g pm2 start npm --name RevampCode --watch -- start
```

OR

```bash
pm2 start server.js --name app1 --watch -- 5000
```

```bash
pm2 start app.js --name app2 --watch -- 5001
```

**PM2 Useful Commands:**

```bash
pm2 list
```

```bash
pm2 stop all
```

```bash
pm2 delete all
```

```bash
pm2 monit
```

```bash
pm2 restart app1
```

```bash
pm2 save
```

---

## 9\. Configure Nginx Reverse Proxy

Default config (drop unwanted requests):

```bash
sudo nano /etc/nginx/sites-available/default
```

```bash
server {
    listen 80 default_server;
        server_name _;
        location / {
        return 444;
        }
}
```

### App 1 Configuration:

```bash
sudo nano /etc/nginx/sites-available/app1

# e.g sudo nano /etc/nginx/sites-available/RevampCode
```

```bash
# For a node app
server {
    listen 80;
    server_name app1.domain.com; # Replace with api.revampcode.com if using subdomain
    location / {
        proxy_pass http://localhost:8080;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

```bash
# HTML website (static files)
server {
    listen 80;
    server_name revampcode.com www.revampcode.com;
    root /var/www/revampcode;
    index index.html;
}

server {
    listen 80;
    server_name mywatch.com www.mywatch.com;
    root /var/www/mywatch;
    index index.html;
}

```

Enable Config:

```bash
sudo ln -s /etc/nginx/sites-available/app1 /etc/nginx/sites-enabled/
```

(Repeat for each app.)

---

## 10\. Reload Nginx

```bash
sudo nginx -t
```

```bash
sudo systemctl reload nginx
```

---

## 11\. Update DNS A Records

| Type     | Name | Value          |
| -------- | ---- | -------------- |
| A Record | app1 | VPS_IP_ADDRESS |
| A Record | app2 | VPS_IP_ADDRESS |

---

## 12\. Setup SSL with Let’s Encrypt

**Install Certbot:**

```bash
sudo apt install certbot python3-certbot-nginx
```

**Obtain Certificates:**

```bash
sudo certbot --nginx -d app1.domain.com -d app2.domain.com
```

**Auto-Renew:**

```bash
sudo certbot renew --dry-run
```

---

## 13\. Enable PM2 on Reboot

```bash
pm2 startup
```

```bash
pm2 save
```

---

## 14\. Monitoring & Maintenance

**Monitor CPU & Memory:**

```bash
htop
```

```bash
free -h
```

**Check Used Ports:**

```bash
sudo lsof -i :8081
```

```bash
sudo kill -9
```

**Misc:**

```bash
passwd username # Change user password
```

&nbsp;

USED PORTS

8080- ScholarshipPulse

8081  - RevampCode

8082 - CSMTech

8083 - Photonics

8084 - RecolorPro

8085 - Qdyizhiyou

8086 - STN

8087 - MzorlineWears

8088 - Dosify

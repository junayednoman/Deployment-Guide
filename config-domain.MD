## Configure domain

**1. Install Nginx (reverse proxy)**
```bash
sudo apt install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
```
- Allow firewall (if UFW enabled):
```bash
sudo ufw allow 'Nginx Full'
```

**2. Configure Nginx for your domain**


- Create config:
```bash
sudo nano /etc/nginx/sites-available/yourdomain.com
```
- Paste this (edit domain & port):
```bash
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;

    location / {
        proxy_pass http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
- Enable site:
```bash
sudo ln -s /etc/nginx/sites-available/yourdomain.com /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

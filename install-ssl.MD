## Install SSL Certificate for domains

**1. Install Certbot**
```bash
sudo apt install certbot python3-certbot-nginx -y
```

**2. Get SSL certificate**
```bash
sudo certbot --nginx -d api.yourdomain.com -d www.api.yourdomain.com
```

**3. Enable auto-renew (usually already on)**
- Check: 
```bash
systemctl status certbot.timer
```
- Test renewal:: 
```bash
sudo certbot renew --dry-run
```

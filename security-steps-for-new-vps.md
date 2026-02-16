## Steps to ensure security of a new vps

**1. Use a strong SSH setup**
- **Disable password login:** only allow key-based authentication and change default ssh port
```bash
sudo nano /etc/ssh/sshd_config
```
- Set:
```bash
PasswordAuthentication no
PermitRootLogin no
Port ***
```
- Edit `50-cloud-init.conf`:
```
sudo nano /etc/ssh/sshd_config.d/50-cloud-init.conf
```

- Allow the new port and keep SSH access:
```bash
sudo ufw allow ***/tcp
sudo ufw reload
```

- Restart SSH
```bash
sudo sshd -t
sudo systemctl restart ssh
```

**2. Keep OS & packages updated**
```bash
sudo apt update && sudo apt upgrade -y
```
- Consider **automatic security** updates:
```bash
sudo apt install unattended-upgrades -y
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

**3. Configure a firewall**
- Ubuntu: UFW (Uncomplicated Firewall)

```bash
sudo ufw allow <ssh-port>
sudo ufw allow 80
sudo ufw allow 443
sudo ufw enable
```
- Don't make **5000, 3000** or other ports where apps are running public. Only open ports you actually need. Everything else blocked.

**4. Config fail2ban**
- Install fail2ban:
```
sudo apt update  
sudo apt install fail2ban -y
```

- Enable & start it:
```
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```
- Check status:
```
sudo systemctl status fail2ban
```
- Create config (don’t edit default file)
```
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```
- Then edit:
```
sudo nano /etc/fail2ban/jail.local
```
- Find `[sshd]` section and make sure:
```
enabled = true
port = ****
maxretry = 5
bantime = 1h
findtime = 10m
```
- Inside `[DEFAULT]` section:
```
ignoreip = 127.0.0.1/8 YOUR_HOME_IP
```
- Restart
```
sudo systemctl restart fail2ban
```
- Check if SSH jail is active
```
sudo fail2ban-client status
sudo fail2ban-client status sshd
```

**5. Reverse proxy & SSL**
- Use Nginx in front of Node.js apps.
- Always enforce **HTTPS** with Let’s Encrypt.
- Redirect HTTP → HTTPS.

**6. Limit server access**
- Only allow necessary IPs to SSH (optional but safer).
- Don’t leave ports open to ```0.0.0.0/0``` if not required.

**7. Monitor your server**
- Install monitoring/logging tools:
    - ```htop``` for RAM/CPU usage
    - ```fail2ban``` to block repeated failed login attempts
    - ```logwatch``` or check ```/var/log``` regularly

**8. Backup & snapshots**
- Always take **EC2 snapshots** or database backups regularly.
- Helps recover from attacks or accidental deletion.

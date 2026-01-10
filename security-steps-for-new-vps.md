## Steps to ensure security of a new vps

**1. Use a strong SSH setup**
- **Disable password login:** only allow key-based authentication.
```bash
sudo nano /etc/ssh/sshd_config
```
- Set:
```bash
PasswordAuthentication no
PermitRootLogin no
```
- **Change default SSH port from 22 to something random(Important ⚠️)**

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
- Only open ports you actually need. Everything else blocked.

**4. Reverse proxy & SSL**
- Use Nginx in front of Node.js apps.
- Always enforce **HTTPS** with Let’s Encrypt.
- Redirect HTTP → HTTPS.

**5. Limit server access**
- Only allow necessary IPs to SSH (optional but safer).
- Don’t leave ports open to ```0.0.0.0/0``` if not required.

**6. Monitor your server**
- Install monitoring/logging tools:
    - ```htop``` for RAM/CPU usage
    - ```fail2ban``` to block repeated failed login attempts
    - ```logwatch``` or check ```/var/log``` regularly

**7. Backup & snapshots**
- Always take **EC2 snapshots** or database backups regularly.
- Helps recover from attacks or accidental deletion.

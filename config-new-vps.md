## Configure fresh vps

**1. Update & upgrade packages**
```bash
sudo apt update && sudo apt upgrade -y
```

**2. Install essentials**
```bash
sudo apt install git curl build-essential -y
```

**3. Install Node.js & npm**
- Use NodeSource for latest stable:

```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs
```

**4. Install PM2 (for managing Node apps)**
```bash
sudo npm install -g pm2
```

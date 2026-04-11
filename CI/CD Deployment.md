# 🚀 CI/CD Deployment Guide (Node.js + PM2 + VPS)

## 📌 High-level flow

1. On every push to `main`, GitHub Actions runs `lint`, `format check (Prettier)`, and `build`.
2. If all pass, a deploy job SSHes into your VPS, pulls latest `main`, installs deps/builds, and restarts PM2.

---

## ⚙️ Step-by-step

### 1. Prepare your app scripts

In `package.json`, ensure these exist:

- `lint`
- `format:check` (Prettier check, not write)
- `build`

Example:

```json
{
  "scripts": {
    "lint": "eslint .",
    "format:check": "prettier --check .",
    "build": "your real build command"
  }
}
```

### 2. Prepare VPS once

- Install Node/npm, git, and pm2.
- Clone repo on VPS (example: `/var/www/myne-dashboard`).

#### Create/start PM2 app (first time)

```bash
pm2 start <start-command> --name myne-dashboard
pm2 save
```

---

### 3. Create SSH deploy key

- On local/VPS, generate a key pair dedicated for deployment.
- Add the **public key** to the VPS user’s `~/.ssh/authorized_keys`.
- Add the **private key** to your GitHub repository secrets as `VPS_SSH_KEY`.

#### Add these GitHub secrets:

- `VPS_HOST`
- `VPS_USER`
- `VPS_PORT` (usually `22`)
- `VPS_APP_DIR` (e.g. `/var/www/myne-dashboard`)

---

### 4. Create GitHub Actions workflow

**File:** `.github/workflows/ci-cd.yml`  
**Trigger:** `on: push` to `main`

#### Job 1: CI

- Checkout code
- Setup Node
- Run `npm ci`
- Run `npm run lint`
- Run `npm run format:check`
- Run `npm run build`

#### Job 2: Deploy (needs CI)

- Runs only if CI passes
- Setup SSH key
- SSH into VPS and execute:

```bash
cd $VPS_APP_DIR
git fetch origin
git reset --hard origin/main   # or git pull origin main
npm ci                         # or npm install --production depending on app
npm run build
pm2 restart myne-dashboard
pm2 save
```

---

### 5. Protect main branch

- Enable branch protection for `main`.
- Require CI checks to pass before merging (recommended).


---

### 6. Test end-to-end

Push a small commit to `main`.

#### Verify:

- CI job passes in GitHub Actions tab.
- Deploy job runs successfully.
- VPS contains the latest commit.
- PM2 process restarts and is healthy.

---

### 7. Add rollback habit (recommended)

- Keep previous release or at least use `git reflog` / commit hash.

If a deployment fails:

```bash
cd /var/www/myne-dashboard
git reflog
git reset --hard <last-good-commit>
pm2 restart myne-dashboard

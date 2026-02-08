# Ansible Docker Deploy

Generic Ansible boilerplate for deploying containerized applications via GitHub Actions.

## Quick Start

### 1. Clone This Repository

```bash
git clone https://github.com/yourusername/ansible-docker-deploy.git
cd ansible-docker-deploy
```

### 2. Setup GitHub Secrets

Go to **Settings → Secrets and variables → Actions → New repository secret**

Add these **8 secrets**:

| Secret Name | Description | Example |
|-------------|-------------|---------|
| `SSH_PRIVATE_KEY` | SSH private key for server | `-----BEGIN OPENSSH PRIVATE KEY-----...` |
| `ANSIBLE_HOST` | Your server IP/hostname | `203.0.113.42` |
| `ANSIBLE_USER` | SSH username | `deploy` or `ubuntu` |
| `DEPLOYMENT_PATH` | Where to deploy | `/opt/myapp` |
| `DEPLOYMENT_USER` | File owner | `www-data` or `deploy` |
| `DOCKERHUB_USERNAME` | Your Docker Hub username | `yourname` |
| `DOCKERHUB_REPOSITORY` | Your Docker repo name | `myapp` |
| `DOCKERHUB_TOKEN` | Docker Hub access token | `dckr_pat_xxxxx` |

<details>
<summary>📖 How to get these values?</summary>

**SSH_PRIVATE_KEY:**
```bash
ssh-keygen -t ed25519 -f ~/.ssh/deploy_key
cat ~/.ssh/deploy_key  # Copy this to GitHub Secret
ssh-copy-id -i ~/.ssh/deploy_key.pub user@your-server
```

**DOCKERHUB_TOKEN:**
1. Login to hub.docker.com
2. Settings → Security → New Access Token
3. Copy token to GitHub Secret

</details>

### 3. Prepare Your Docker Images

**Your images must use these tags:**
- `yourname/yourrepo:frontend`
- `yourname/yourrepo:backend`

Build and push:
```bash
docker build -t yourname/yourrepo:frontend ./frontend
docker build -t yourname/yourrepo:backend ./backend

docker push yourname/yourrepo:frontend
docker push yourname/yourrepo:backend
```

### 4. Deploy!

1. Go to **Actions** tab in GitHub
2. Click **Deploy Application**
3. Click **Run workflow**
4. Type `DEPLOY` to confirm
5. Click **Run workflow**

Done! ✅

## What It Does

- Connects to your server via SSH
- Pulls your Docker images from Docker Hub
- Deploys frontend (port 80) and backend (port 3000)
- Restarts containers automatically

## Customization

Edit `vars/defaults.yml` to change:
- Port numbers
- Container names
- Timeouts
- Cleanup settings

## Rollback

If something goes wrong:

```bash
ansible-playbook playbooks/rollback.yml \
  -i inventory.ini \
  -e "ansible_host=YOUR_SERVER_IP" \
  -e "ansible_user=YOUR_SSH_USER" \
  -e "deployment_path=/opt/yourapp"
```

## Requirements

- A server with Docker installed
- GitHub repository with Actions enabled
- Docker Hub account

## Support

Issues? Check:
- GitHub Actions logs (Actions tab)
- SSH connection: `ssh -i ~/.ssh/deploy_key user@server`
- Docker Hub: Ensure images exist with correct tags

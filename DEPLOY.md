# Pole Finder - Deployment Guide

## Option 1: Digital Ocean App Platform (Easiest)

### Via GitHub (Recommended)
1. Push this folder to a GitHub repo
2. Go to https://cloud.digitalocean.com/apps
3. Click "Create App"
4. Select "GitHub" and authorize
5. Choose your repo
6. It auto-detects static site - click "Next"
7. Choose plan: **Starter (Free)** for static sites
8. Click "Create Resources"
9. Done! You'll get a URL like `pole-finder-xxxxx.ondigitalocean.app`

### Via Direct Upload
1. Go to https://cloud.digitalocean.com/apps
2. Click "Create App"
3. Select "Upload" as source
4. Drag the `pole-finder-app` folder
5. Follow the prompts

---

## Option 2: Digital Ocean Spaces (CDN)

Good for custom domain + CDN distribution:

```bash
# Install doctl CLI
brew install doctl  # or download from DO

# Authenticate
doctl auth init

# Create Space
doctl compute spaces create pole-finder --region nyc3

# Upload
doctl compute spaces put pole-finder index.html --acl public-read
```

Then enable CDN in the Spaces settings.

---

## Option 3: Droplet with Nginx (Full Control)

```bash
# Create droplet (Ubuntu 24.04, $4/mo)
doctl compute droplet create pole-finder \
  --image ubuntu-24-04-x64 \
  --size s-1vcpu-512mb-10gb \
  --region nyc1

# SSH in
ssh root@YOUR_DROPLET_IP

# Install nginx
apt update && apt install -y nginx

# Copy files
scp index.html root@YOUR_DROPLET_IP:/var/www/html/

# Done - visit http://YOUR_DROPLET_IP
```

---

## Custom Domain Setup

1. In DO App Platform, go to Settings > Domains
2. Add your domain (e.g., `polefinder.evrdaytech.com`)
3. Add CNAME record at your DNS provider:
   - Name: `polefinder`
   - Value: `pole-finder-xxxxx.ondigitalocean.app`
4. SSL is automatic

---

## Quick Deploy Commands

```bash
# If you have doctl + GitHub CLI installed:
cd pole-finder-app
gh repo create pole-finder --public --source=. --push
doctl apps create --spec .do/app.yaml
```

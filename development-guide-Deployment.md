# Panduan Deployment - Bengkel Koding Web V2

## Daftar Isi

1. [Deployment Strategy](#1-deployment-strategy)
2. [CI/CD Pipeline](#2-cicd-pipeline)
3. [Manual Deployment](#3-manual-deployment)
4. [PM2 Deployment](#4-pm2-deployment)
5. [Server Configuration](#5-server-configuration)
6. [Environment Setup](#6-environment-setup)
7. [Monitoring & Maintenance](#7-monitoring--maintenance)
8. [Rollback Strategy](#8-rollback-strategy)

---

## 1. Deployment Strategy

### 1.1 Deployment Environments

Project ini memiliki 3 environment deployment:

| Environment     | Purpose                | URL                                      | Branch        | Auto Deploy |
| --------------- | ---------------------- | ---------------------------------------- | ------------- | ----------- |
| **Development** | Testing & development  | http://dev.bengkelkoding.dinus.ac.id     | `development` | Yes         |
| **Staging**     | Pre-production testing | http://staging.bengkelkoding.dinus.ac.id | `staging`     | Yes         |
| **Production**  | Live application       | https://bengkelkoding.dinus.ac.id        | `main`        | Yes         |

### 1.2 Deployment Flow

```mermaid
graph LR
    A[Local Development] -->|git push| B[GitHub Repository]
    B -->|Webhook| C[CI/CD Pipeline]

    C -->|development branch| D[Dev Server]
    C -->|staging branch| E[Staging Server]
    C -->|main branch| F[Production Server]

    D -->|Build & Test| G[Deploy Dev]
    E -->|Build & Test| H[Deploy Staging]
    F -->|Build & Test & Approval| I[Deploy Production]

    G --> J[PM2 Restart]
    H --> K[PM2 Restart]
    I --> L[PM2 Restart]
```

### 1.3 Server Locations

#### Development Server

```
Hostname: dev-bengkelkoding-web-v2.dinus.ac.id
IP: 192.168.10.100 (internal)
Path: /var/www/bengkelkoding-web-v2/development
User: deployer
Port: 3001
PM2 Name: bengkelkoding-dev
```

#### Production Server

```
Hostname: bengkelkoding-web-v2.dinus.ac.id
IP: 103.XX.XXX.XXX (public)
Path: /var/www/bengkelkoding-web-v2/production
User: deployer
Port: 3000
PM2 Name: bengkelkoding-prod
```

---

## 2. CI/CD Pipeline

### 2.1 Pipeline Overview

Project ini menggunakan **GitHub Actions** untuk CI/CD automation.

**Alur CI/CD**:

1. Developer push code ke GitHub
2. GitHub Actions trigger workflow
3. Run automated tests
4. Build Next.js application
5. Deploy ke server via SSH
6. Restart PM2 process
7. Verify deployment
8. Send notification (success/failure)

### 2.2 GitHub Actions Configuration

**File**: `.github/workflows/deploy.yml`

```yaml
name: Deploy Bengkel Koding Web V2

on:
  push:
    branches:
      - development # Auto deploy to dev
      - staging # Auto deploy to staging
      - main # Auto deploy to production

jobs:
  # Job 1: Build and Test
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Setup Bun
        uses: oven-sh/setup-bun@v1
        with:
          bun-version: latest

      - name: Install dependencies
        run: bun install

      - name: Run linter
        run: bun run lint

      - name: Type check
        run: bun run type-check

      - name: Build application
        run: bun run build
        env:
          NEXT_PUBLIC_API_URL_BENGKEL_KODING: ${{ secrets.API_URL }}
          NEXT_PUBLIC_RECAPTCHA_SITE_KEY: ${{ secrets.RECAPTCHA_SITE_KEY }}

      - name: Upload build artifacts
        uses: actions/upload-artifact@v3
        with:
          name: build-files
          path: |
            .next
            public
            package.json
            bun.lockb

  # Job 2: Deploy to Server
  deploy:
    needs: build
    runs-on: ubuntu-latest

    steps:
      - name: Download build artifacts
        uses: actions/download-artifact@v3
        with:
          name: build-files

      - name: Deploy to server
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.SERVER_HOST }}
          username: ${{ secrets.SERVER_USER }}
          key: ${{ secrets.SSH_PRIVATE_KEY }}
          port: 22
          script: |
            cd /var/www/bengkelkoding-web-v2/${{ github.ref_name }}

            # Pull latest code
            git fetch origin
            git reset --hard origin/${{ github.ref_name }}

            # Install dependencies
            bun install --production

            # Build application
            bun run build

            # Restart PM2
            pm2 restart bengkelkoding-${{ github.ref_name }}

            # Save PM2 state
            pm2 save

      - name: Verify deployment
        run: |
          sleep 10
          curl -f ${{ secrets.DEPLOY_URL }}/api/health || exit 1

      - name: Notify deployment
        uses: 8398a7/action-slack@v3
        with:
          status: ${{ job.status }}
          text: "Deployment ${{ github.ref_name }}: ${{ job.status }}"
          webhook_url: ${{ secrets.SLACK_WEBHOOK }}
        if: always()
```

### 2.3 GitHub Secrets Configuration

**Settings → Secrets and variables → Actions**

Required secrets:

| Secret Name          | Description                       | Example                                 |
| -------------------- | --------------------------------- | --------------------------------------- |
| `API_URL`            | Backend API URL                   | `https://api.bengkelkoding.dinus.ac.id` |
| `RECAPTCHA_SITE_KEY` | Google reCAPTCHA site key         | `6Lc...`                                |
| `SERVER_HOST`        | Server hostname/IP                | `bengkelkoding-web-v2.dinus.ac.id`      |
| `SERVER_USER`        | SSH username                      | `deployer`                              |
| `SSH_PRIVATE_KEY`    | SSH private key for deployment    | `-----BEGIN RSA PRIVATE KEY-----...`    |
| `DEPLOY_URL`         | Deployment URL untuk verification | `https://bengkelkoding.dinus.ac.id`     |
| `SLACK_WEBHOOK`      | Slack webhook untuk notifikasi    | `https://hooks.slack.com/services/...`  |

**Cara add secret**:

1. Go to repository → Settings → Secrets
2. Click "New repository secret"
3. Input name dan value
4. Click "Add secret"

### 2.4 Branch Protection Rules

**Settings → Branches → Branch protection rules**

**For `main` branch (production)**:

- ✅ Require pull request before merging
- ✅ Require approvals (minimum 1)
- ✅ Require status checks to pass
  - ✅ Build
  - ✅ Lint
  - ✅ Type check
- ✅ Require conversation resolution
- ✅ Include administrators

**For `development` branch**:

- ✅ Require status checks to pass
  - ✅ Build

---

## 3. Manual Deployment

### 3.1 Prerequisites

**Di local machine**:

- SSH access ke server
- SSH key sudah di-setup
- Git configured dengan SSH

**Setup SSH Key**:

```bash
# Generate SSH key (jika belum punya)
ssh-keygen -t ed25519 -C "your_email@example.com"

# Copy public key ke server
ssh-copy-id deployer@bengkelkoding-web-v2.dinus.ac.id

# Test connection
ssh deployer@bengkelkoding-web-v2.dinus.ac.id
```

### 3.2 Deploy ke Development

```bash
# 1. SSH ke dev server
ssh deployer@dev-bengkelkoding-web-v2.dinus.ac.id

# 2. Navigate ke project directory
cd /var/www/bengkelkoding-web-v2/development

# 3. Pull latest code
git fetch origin
git pull origin development

# 4. Install dependencies
bun install

# 5. Build application
bun run build

# 6. Restart PM2
pm2 restart bengkelkoding-dev

# 7. Check status
pm2 status
pm2 logs bengkelkoding-dev --lines 50

# 8. Exit SSH
exit
```

### 3.3 Deploy ke Production

```bash
# 1. SSH ke production server
ssh deployer@bengkelkoding-web-v2.dinus.ac.id

# 2. Navigate ke project directory
cd /var/www/bengkelkoding-web-v2/production

# 3. Backup current version (optional but recommended)
tar -czf backups/backup-$(date +%Y%m%d-%H%M%S).tar.gz .next package.json

# 4. Pull latest code
git fetch origin
git pull origin main

# 5. Install dependencies
bun install --production

# 6. Build application
bun run build

# 7. Restart PM2 with zero-downtime
pm2 reload bengkelkoding-prod

# 8. Check status
pm2 status
pm2 logs bengkelkoding-prod --lines 50

# 9. Verify application is running
curl http://localhost:3000/api/health

# 10. Exit SSH
exit
```

### 3.4 Deploy dari Local (Advanced)

**Create deploy script**: `scripts/deploy.sh`

```bash
#!/bin/bash

# Deploy script for Bengkel Koding Web V2

set -e  # Exit on error

# Configuration
ENVIRONMENT=$1
if [ -z "$ENVIRONMENT" ]; then
  echo "Usage: ./deploy.sh [dev|staging|prod]"
  exit 1
fi

case $ENVIRONMENT in
  dev)
    SERVER="deployer@dev-bengkelkoding-web-v2.dinus.ac.id"
    PATH="/var/www/bengkelkoding-web-v2/development"
    BRANCH="development"
    PM2_NAME="bengkelkoding-dev"
    ;;
  staging)
    SERVER="deployer@staging-bengkelkoding-web-v2.dinus.ac.id"
    PATH="/var/www/bengkelkoding-web-v2/staging"
    BRANCH="staging"
    PM2_NAME="bengkelkoding-staging"
    ;;
  prod)
    SERVER="deployer@bengkelkoding-web-v2.dinus.ac.id"
    PATH="/var/www/bengkelkoding-web-v2/production"
    BRANCH="main"
    PM2_NAME="bengkelkoding-prod"

    # Production confirmation
    read -p "Deploy to PRODUCTION? (yes/no): " CONFIRM
    if [ "$CONFIRM" != "yes" ]; then
      echo "Deployment cancelled"
      exit 0
    fi
    ;;
  *)
    echo "Invalid environment: $ENVIRONMENT"
    exit 1
    ;;
esac

echo "🚀 Deploying to $ENVIRONMENT..."

# SSH and deploy
ssh $SERVER << ENDSSH
  set -e

  echo "📁 Navigating to project directory..."
  cd $PATH

  echo "📥 Pulling latest code from $BRANCH..."
  git fetch origin
  git pull origin $BRANCH

  echo "📦 Installing dependencies..."
  bun install

  echo "🔨 Building application..."
  bun run build

  echo "🔄 Restarting PM2..."
  pm2 restart $PM2_NAME

  echo "💾 Saving PM2 state..."
  pm2 save

  echo "✅ Deployment completed!"
  pm2 status
ENDSSH

echo "🎉 Deployment to $ENVIRONMENT successful!"
```

**Cara pakai**:

```bash
# Make executable
chmod +x scripts/deploy.sh

# Deploy to dev
./scripts/deploy.sh dev

# Deploy to production
./scripts/deploy.sh prod
```

---

## 4. PM2 Deployment

### 4.1 PM2 Setup

**Install PM2** (di server):

```bash
# Install PM2 globally
npm install -g pm2

# Check version
pm2 --version
```

### 4.2 PM2 Configuration

**File**: `ecosystem.config.js` (di server)

```javascript
module.exports = {
  apps: [
    {
      // Development
      name: "bengkelkoding-dev",
      script: "bun",
      args: "run start",
      cwd: "/var/www/bengkelkoding-web-v2/development",
      instances: 1,
      exec_mode: "fork",
      watch: false,
      max_memory_restart: "500M",
      env: {
        NODE_ENV: "development",
        PORT: 3001,
      },
      error_file: "/var/log/pm2/bengkelkoding-dev-error.log",
      out_file: "/var/log/pm2/bengkelkoding-dev-out.log",
      log_file: "/var/log/pm2/bengkelkoding-dev-combined.log",
      time: true,
    },
    {
      // Staging
      name: "bengkelkoding-staging",
      script: "bun",
      args: "run start",
      cwd: "/var/www/bengkelkoding-web-v2/staging",
      instances: 2,
      exec_mode: "cluster",
      watch: false,
      max_memory_restart: "1G",
      env: {
        NODE_ENV: "production",
        PORT: 3002,
      },
      error_file: "/var/log/pm2/bengkelkoding-staging-error.log",
      out_file: "/var/log/pm2/bengkelkoding-staging-out.log",
      log_file: "/var/log/pm2/bengkelkoding-staging-combined.log",
      time: true,
    },
    {
      // Production
      name: "bengkelkoding-prod",
      script: "bun",
      args: "run start",
      cwd: "/var/www/bengkelkoding-web-v2/production",
      instances: 4, // 4 instances untuk load balancing
      exec_mode: "cluster",
      watch: false,
      max_memory_restart: "1G",
      env: {
        NODE_ENV: "production",
        PORT: 3000,
      },
      error_file: "/var/log/pm2/bengkelkoding-prod-error.log",
      out_file: "/var/log/pm2/bengkelkoding-prod-out.log",
      log_file: "/var/log/pm2/bengkelkoding-prod-combined.log",
      time: true,

      // Advanced options
      kill_timeout: 5000,
      listen_timeout: 10000,
      shutdown_with_message: true,

      // Monitoring
      min_uptime: "10s",
      max_restarts: 10,
      autorestart: true,
    },
  ],
};
```

### 4.3 PM2 Commands

#### Start Application

```bash
# Start dengan config file
pm2 start ecosystem.config.js

# Start specific app
pm2 start ecosystem.config.js --only bengkelkoding-prod

# Start manual (tanpa config)
cd /var/www/bengkelkoding-web-v2/production
pm2 start "bun run start" --name bengkelkoding-prod -i 4
```

#### Stop/Restart Application

```bash
# Restart
pm2 restart bengkelkoding-prod

# Reload (zero-downtime)
pm2 reload bengkelkoding-prod

# Stop
pm2 stop bengkelkoding-prod

# Delete
pm2 delete bengkelkoding-prod
```

#### Monitor Application

```bash
# List all apps
pm2 list

# Monitor dashboard
pm2 monit

# Show app info
pm2 show bengkelkoding-prod

# View logs
pm2 logs bengkelkoding-prod

# View logs (last 100 lines)
pm2 logs bengkelkoding-prod --lines 100

# View only errors
pm2 logs bengkelkoding-prod --err

# Clear logs
pm2 flush
```

#### Save Configuration

```bash
# Save current process list
pm2 save

# Auto-start on system boot
pm2 startup

# Copy & run the command it gives you
# Example output:
sudo env PATH=$PATH:/usr/bin pm2 startup systemd -u deployer --hp /home/deployer

# Disable startup
pm2 unstartup systemd
```

### 4.4 PM2 Log Rotation

**Setup log rotation** untuk prevent logs dari menjadi terlalu besar:

```bash
# Install PM2 log rotate module
pm2 install pm2-logrotate

# Configure log rotation
pm2 set pm2-logrotate:max_size 10M        # Max size 10MB
pm2 set pm2-logrotate:retain 30           # Keep 30 old logs
pm2 set pm2-logrotate:compress true       # Compress old logs
pm2 set pm2-logrotate:dateFormat YYYY-MM-DD_HH-mm-ss
pm2 set pm2-logrotate:rotateModule true   # Rotate PM2 module logs
```

### 4.5 PM2 Monitoring (Optional)

**PM2 Plus** untuk advanced monitoring:

```bash
# Link to PM2 Plus
pm2 link <secret_key> <public_key>

# Or use local monitoring
pm2 web

# Access at http://localhost:9615
```

---

## 5. Server Configuration

### 5.1 Nginx Configuration

**File**: `/etc/nginx/sites-available/bengkelkoding-web-v2`

#### Development Server

```nginx
server {
    listen 80;
    server_name dev.bengkelkoding.dinus.ac.id;

    location / {
        proxy_pass http://localhost:3001;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }
}
```

#### Production Server (with SSL)

```nginx
# HTTP to HTTPS redirect
server {
    listen 80;
    server_name bengkelkoding.dinus.ac.id www.bengkelkoding.dinus.ac.id;
    return 301 https://$server_name$request_uri;
}

# HTTPS
server {
    listen 443 ssl http2;
    server_name bengkelkoding.dinus.ac.id www.bengkelkoding.dinus.ac.id;

    # SSL certificates (Let's Encrypt)
    ssl_certificate /etc/letsencrypt/live/bengkelkoding.dinus.ac.id/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/bengkelkoding.dinus.ac.id/privkey.pem;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;

    # Security headers
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;

    # Gzip compression
    gzip on;
    gzip_vary on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    # Static file caching
    location /_next/static {
        proxy_pass http://localhost:3000;
        proxy_cache_valid 200 30d;
        add_header Cache-Control "public, immutable";
    }

    # Image caching
    location ~* \.(jpg|jpeg|png|gif|ico|svg|webp)$ {
        proxy_pass http://localhost:3000;
        proxy_cache_valid 200 7d;
        add_header Cache-Control "public, max-age=604800";
    }

    # Main proxy
    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;

        # Timeouts
        proxy_connect_timeout 60s;
        proxy_send_timeout 60s;
        proxy_read_timeout 60s;
    }

    # Rate limiting for API
    location /api/ {
        limit_req zone=api_limit burst=20 nodelay;
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}

# Rate limiting zone (add to http block in nginx.conf)
# limit_req_zone $binary_remote_addr zone=api_limit:10m rate=10r/s;
```

**Activate configuration**:

```bash
# Test configuration
sudo nginx -t

# Enable site
sudo ln -s /etc/nginx/sites-available/bengkelkoding-web-v2 /etc/nginx/sites-enabled/

# Reload Nginx
sudo systemctl reload nginx
```

### 5.2 SSL Certificate (Let's Encrypt)

```bash
# Install Certbot
sudo apt update
sudo apt install certbot python3-certbot-nginx

# Obtain certificate
sudo certbot --nginx -d bengkelkoding.dinus.ac.id -d www.bengkelkoding.dinus.ac.id

# Auto-renewal (already set up by certbot)
# Test renewal
sudo certbot renew --dry-run

# Certificate will auto-renew via cron job
```

### 5.3 Firewall Configuration

```bash
# Check UFW status
sudo ufw status

# Allow SSH (important!)
sudo ufw allow 22/tcp

# Allow HTTP & HTTPS
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# Enable firewall
sudo ufw enable

# Check status
sudo ufw status verbose
```

---

## 6. Environment Setup

### 6.1 Development Environment

**File**: `/var/www/bengkelkoding-web-v2/development/.env.local`

```bash
# API Configuration
NEXT_PUBLIC_API_URL_BENGKEL_KODING=http://dev-api.bengkelkoding.dinus.ac.id

# Image Assets
NEXT_PUBLIC_IMG_ASSET_URL_BENGKEL_KODING=http://dev-api.bengkelkoding.dinus.ac.id/storage

# Google reCAPTCHA (use test keys for dev)
NEXT_PUBLIC_RECAPTCHA_SITE_KEY=6LeIxAcTAAAAAJcZVRqyHh71UMIEGNQ_MXjiZKhI
NEXT_PUBLIC_RECAPTCHA_SECRET_KEY=6LeIxAcTAAAAAGG-vFI1TnRWxMZNFuojJ4WifJWe

# Image Domains
NEXT_PUBLIC_IMAGE_DOMAIN_1=dev-api.bengkelkoding.dinus.ac.id
NEXT_PUBLIC_IMAGE_DOMAIN_2=localhost

# Environment
NODE_ENV=development
PORT=3001
```

### 6.2 Production Environment

**File**: `/var/www/bengkelkoding-web-v2/production/.env.local`

```bash
# API Configuration
NEXT_PUBLIC_API_URL_BENGKEL_KODING=https://api.bengkelkoding.dinus.ac.id

# Image Assets
NEXT_PUBLIC_IMG_ASSET_URL_BENGKEL_KODING=https://api.bengkelkoding.dinus.ac.id/storage

# Google reCAPTCHA (production keys)
NEXT_PUBLIC_RECAPTCHA_SITE_KEY=6Lc_your_actual_site_key_here
NEXT_PUBLIC_RECAPTCHA_SECRET_KEY=6Lc_your_actual_secret_key_here

# Image Domains
NEXT_PUBLIC_IMAGE_DOMAIN_1=api.bengkelkoding.dinus.ac.id
NEXT_PUBLIC_IMAGE_DOMAIN_2=bengkelkoding.dinus.ac.id

# Environment
NODE_ENV=production
PORT=3000

# Performance & Security
NEXT_TELEMETRY_DISABLED=1
```

**⚠️ Security Notes**:

- **NEVER commit** `.env.local` ke Git
- Use `.env.local.example` untuk template
- Store secrets in secure location (e.g., 1Password, Vault)
- Rotate keys periodically

---

## 7. Monitoring & Maintenance

### 7.1 Health Checks

**Create health check endpoint**: `app/api/health/route.ts`

```typescript
import { NextResponse } from "next/server";

export async function GET() {
  try {
    // Check database connection
    // const dbStatus = await checkDatabase();

    return NextResponse.json({
      status: "healthy",
      timestamp: new Date().toISOString(),
      uptime: process.uptime(),
      environment: process.env.NODE_ENV,
      // database: dbStatus,
    });
  } catch (error) {
    return NextResponse.json(
      {
        status: "unhealthy",
        error: error.message,
      },
      { status: 503 }
    );
  }
}
```

**Monitor health**:

```bash
# Check health
curl https://bengkelkoding.dinus.ac.id/api/health

# Response:
{
  "status": "healthy",
  "timestamp": "2025-01-15T10:30:00.000Z",
  "uptime": 86400,
  "environment": "production"
}
```

### 7.2 Server Monitoring

**Check server resources**:

```bash
# CPU & Memory
htop

# Disk usage
df -h

# PM2 monitoring
pm2 monit

# Nginx status
sudo systemctl status nginx

# View Nginx logs
sudo tail -f /var/log/nginx/access.log
sudo tail -f /var/log/nginx/error.log
```

### 7.3 Application Monitoring

**PM2 monitoring**:

```bash
# Real-time monitoring
pm2 monit

# List processes with metrics
pm2 status

# Detailed info
pm2 show bengkelkoding-prod

# Memory usage
pm2 describe bengkelkoding-prod | grep memory

# CPU usage
pm2 describe bengkelkoding-prod | grep cpu
```

**View logs**:

```bash
# All logs
pm2 logs

# Specific app
pm2 logs bengkelkoding-prod

# Error logs only
pm2 logs bengkelkoding-prod --err

# Last 200 lines
pm2 logs bengkelkoding-prod --lines 200

# Follow logs (real-time)
pm2 logs bengkelkoding-prod --lines 0
```

### 7.4 Automated Monitoring

**Setup cron job untuk health check**:

```bash
# Edit crontab
crontab -e

# Add health check every 5 minutes
*/5 * * * * curl -f https://bengkelkoding.dinus.ac.id/api/health || echo "Health check failed" | mail -s "Bengkel Koding Alert" admin@bengkelkoding.dinus.ac.id
```

**Setup disk space monitoring**:

```bash
# Check disk space script
#!/bin/bash
THRESHOLD=80
CURRENT=$(df / | tail -1 | awk '{print $5}' | sed 's/%//')

if [ $CURRENT -gt $THRESHOLD ]; then
  echo "Disk usage is ${CURRENT}%" | mail -s "Disk Space Alert" admin@bengkelkoding.dinus.ac.id
fi
```

### 7.5 Backup Strategy

**Daily backup script**:

```bash
#!/bin/bash
# backup.sh

DATE=$(date +%Y%m%d-%H%M%S)
BACKUP_DIR="/var/backups/bengkelkoding-web-v2"
SOURCE="/var/www/bengkelkoding-web-v2/production"

# Create backup directory
mkdir -p $BACKUP_DIR

# Backup application files
tar -czf $BACKUP_DIR/app-$DATE.tar.gz \
  -C $SOURCE \
  --exclude=node_modules \
  --exclude=.next \
  .

# Keep only last 7 days of backups
find $BACKUP_DIR -type f -mtime +7 -delete

echo "Backup completed: app-$DATE.tar.gz"
```

**Setup cron**:

```bash
# Daily backup at 2 AM
0 2 * * * /usr/local/bin/backup.sh
```

---

## 8. Rollback Strategy

### 8.1 Quick Rollback

**Jika deployment gagal**, rollback ke versi sebelumnya:

```bash
# 1. SSH ke server
ssh deployer@bengkelkoding-web-v2.dinus.ac.id

# 2. Navigate to project
cd /var/www/bengkelkoding-web-v2/production

# 3. Check current commit
git log --oneline -5

# 4. Rollback to previous commit
git reset --hard HEAD~1

# Or rollback to specific commit
git reset --hard abc123

# 5. Rebuild
bun install
bun run build

# 6. Restart PM2
pm2 restart bengkelkoding-prod

# 7. Verify
curl http://localhost:3000/api/health
```

### 8.2 Using Git Tags

**Tag stable releases**:

```bash
# In local repository
git tag -a v1.0.0 -m "Release version 1.0.0"
git push origin v1.0.0

# In server
git fetch --tags
git checkout tags/v1.0.0
bun install
bun run build
pm2 restart bengkelkoding-prod
```

### 8.3 Using Backup Files

```bash
# 1. List backups
ls -lh /var/backups/bengkelkoding-web-v2/

# 2. Extract backup
cd /var/www/bengkelkoding-web-v2/production
tar -xzf /var/backups/bengkelkoding-web-v2/app-20250115-020000.tar.gz

# 3. Rebuild
bun install
bun run build

# 4. Restart
pm2 restart bengkelkoding-prod
```

### 8.4 Blue-Green Deployment (Advanced)

**Untuk zero-downtime deployment**:

```bash
# Directory structure
/var/www/bengkelkoding-web-v2/
  ├── blue/      # Current production
  ├── green/     # New deployment
  └── current -> blue  # Symlink

# Deploy to green
cd /var/www/bengkelkoding-web-v2/green
git pull origin main
bun install
bun run build

# Start new instance
pm2 start ecosystem.config.js --name bengkelkoding-green

# Test green
curl http://localhost:3003/api/health

# Switch symlink
ln -sfn green /var/www/bengkelkoding-web-v2/current

# Update Nginx to use new port
# Reload Nginx
sudo systemctl reload nginx

# Stop old instance
pm2 stop bengkelkoding-prod
```

---

## 9. Troubleshooting

### 9.1 PM2 Won't Start

**Problem**: PM2 not starting application

**Solutions**:

```bash
# Check PM2 logs
pm2 logs bengkelkoding-prod --err

# Check if port is in use
sudo lsof -i :3000

# Kill process using port
kill -9 <PID>

# Restart PM2 daemon
pm2 kill
pm2 resurrect
```

### 9.2 Build Failed

**Problem**: `bun run build` fails

**Solutions**:

```bash
# Clear cache
rm -rf .next

# Reinstall dependencies
rm -rf node_modules
rm bun.lockb
bun install

# Check environment variables
cat .env.local

# Try build with verbose
bun run build --debug
```

### 9.3 High Memory Usage

**Problem**: Application consuming too much memory

**Solutions**:

```bash
# Check memory
pm2 status

# Restart app
pm2 restart bengkelkoding-prod

# Set memory limit
pm2 start ecosystem.config.js --max-memory-restart 1G

# Scale down instances
pm2 scale bengkelkoding-prod 2
```

### 9.4 Slow Response Time

**Check**:

1. Server resources: `htop`
2. Network latency: `ping api.bengkelkoding.dinus.ac.id`
3. Application logs: `pm2 logs`
4. Nginx logs: `sudo tail -f /var/log/nginx/access.log`

**Solutions**:

- Enable caching
- Optimize database queries
- Use CDN for static assets
- Scale up PM2 instances

---

## 10. Deployment Checklist

### Pre-Deployment

- [ ] Code reviewed and approved
- [ ] All tests passing
- [ ] No console errors
- [ ] Environment variables configured
- [ ] Database migrations applied
- [ ] Backup created

### During Deployment

- [ ] Pull latest code
- [ ] Install dependencies
- [ ] Build successful
- [ ] PM2 restarted
- [ ] Health check passing
- [ ] Logs checked

### Post-Deployment

- [ ] Application accessible
- [ ] All features working
- [ ] No errors in logs
- [ ] Performance acceptable
- [ ] Monitoring active
- [ ] Team notified

---

## 11. Useful Commands Reference

### Git Commands

```bash
git pull origin main              # Pull latest code
git fetch --tags                  # Fetch all tags
git checkout tags/v1.0.0         # Checkout specific tag
git log --oneline -10            # View recent commits
git reset --hard HEAD~1          # Rollback one commit
```

### PM2 Commands

```bash
pm2 start ecosystem.config.js    # Start all apps
pm2 restart bengkelkoding-prod   # Restart app
pm2 reload bengkelkoding-prod    # Zero-downtime restart
pm2 stop bengkelkoding-prod      # Stop app
pm2 delete bengkelkoding-prod    # Remove app
pm2 logs bengkelkoding-prod      # View logs
pm2 monit                        # Monitor dashboard
pm2 save                         # Save process list
```

### System Commands

```bash
df -h                            # Disk usage
free -h                          # Memory usage
htop                             # System monitor
netstat -tulpn | grep :3000     # Check port usage
sudo systemctl status nginx      # Nginx status
sudo systemctl reload nginx      # Reload Nginx
```

---

**Happy Deploying! 🚀**

---

[← Back: Error Handling](development-guide-Error-Handling.md) | [Home](Home.md)

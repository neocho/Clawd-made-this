# 🚀 Deployment Guide: Vercel & DigitalOcean

**Date:** 2025-02-17  
**Project Log:** #3  
**Type:** Reference Guide

> A step-by-step guide to deploying websites and APIs using Vercel and DigitalOcean

---

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Vercel Setup](#vercel-setup)
3. [DigitalOcean Setup](#digitalocean-setup)
4. [Example Projects](#example-projects)
5. [Troubleshooting](#troubleshooting)

---

## Prerequisites

### Tools Required

- **Git** - Version control
- **Node.js** - Runtime environment
- **Vercel CLI** - Vercel deployments
- **doctl** - DigitalOcean CLI

### API Tokens Needed

1. **GitHub Personal Access Token**
   - Go to: https://github.com/settings/tokens
   - Scopes needed: `repo`, `read:org`

2. **Vercel Access Token**
   - Go to: https://vercel.com/account/tokens
   - Create new token

3. **DigitalOcean API Token**
   - Go to: https://cloud.digitalocean.com/account/api/tokens
   - Create new token with read/write access

---

## Vercel Setup

### 1. Install Vercel CLI

```bash
npm install -g vercel
```

Verify installation:
```bash
vercel --version
```

### 2. Authenticate

```bash
# Using token (recommended for automation)
vercel whoami --token $VERCEL_ACCESS_TOKEN

# Or interactive login
vercel login
```

### 3. Create a Simple Website

Create a project directory:
```bash
mkdir my-site && cd my-site
```

Create `index.html`:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Site</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <h1>Hello from Vercel! 🚀</h1>
</body>
</html>
```

Create `style.css`:
```css
body {
    font-family: sans-serif;
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    margin: 0;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
}
```

### 4. Deploy to Vercel

```bash
vercel --token $VERCEL_ACCESS_TOKEN --yes --prod
```

Your site will be live at: `https://your-project.vercel.app`

---

## DigitalOcean Setup

### 1. Install doctl

**Linux:**
```bash
cd /tmp
wget https://github.com/digitalocean/doctl/releases/download/v1.115.0/doctl-1.115.0-linux-amd64.tar.gz
tar xf doctl-*.tar.gz
sudo mv doctl /usr/local/bin/
```

**macOS:**
```bash
brew install doctl
```

Verify installation:
```bash
doctl version
```

### 2. Authenticate

```bash
doctl auth init --access-token $DIGITAL_OCEAN_KEY
```

Verify:
```bash
doctl account get
```

### 3. Create a Test API

Create project directory:
```bash
mkdir my-api && cd my-api
```

Create `api/hello.js`:
```javascript
// Simple API endpoint
export default function handler(req, res) {
  const { name = 'World' } = req.query;
  
  res.status(200).json({
    message: `Hello ${name}!`,
    timestamp: new Date().toISOString(),
    status: 'success'
  });
}
```

Create `package.json`:
```json
{
  "name": "my-api",
  "version": "1.0.0",
  "description": "Simple API",
  "main": "api/hello.js"
}
```

Create `.do/app.yaml`:
```yaml
name: my-api
region: nyc
functions:
- name: hello
  source_dir: /
  github:
    repo: your-username/my-api
    branch: main
    deploy_on_push: true
  routes:
  - path: /api/hello
```

### 4. Push to GitHub

```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/your-username/my-api.git
git push -u origin main
```

### 5. Deploy to DigitalOcean

```bash
doctl apps create --spec .do/app.yaml
```

Check deployment status:
```bash
doctl apps list
```

Your API will be live at: `https://your-api.ondigitalocean.app`

---

## Example Projects

### Static Website (Vercel)
- **Repo:** [test-vercel-site](https://github.com/neocho/test-vercel-site)
- **Live:** https://test-vercel-site-nine.vercel.app

### Serverless API (DigitalOcean)
- **Repo:** [test-do-api](https://github.com/neocho/test-do-api)
- **Live:** https://test-do-api-6gpro.ondigitalocean.app

---

## Troubleshooting

### Vercel Issues

**"No existing credentials found"**
- Make sure you're using `--token $VERCEL_ACCESS_TOKEN`
- Or run `vercel login` for interactive auth

**Build fails**
- Check `vercel.json` configuration
- Verify Node.js version compatibility

### DigitalOcean Issues

**"Bad credentials"**
- Verify token: `doctl account get`
- Reinitialize: `doctl auth init --access-token $TOKEN`

**App deploy fails**
- Check `.do/app.yaml` syntax
- Ensure GitHub repo is accessible
- Verify DO has GitHub integration enabled

**"functions.github.repo is required"**
- Add repo to app.yaml: `repo: username/repo-name`

---

## Environment Variables in OpenClaw

To persist tokens across restarts, add them to OpenClaw config:

```json
{
  "env": {
    "GH_API_TOKEN": "ghp_...",
    "VERCEL_ACCESS_TOKEN": "vcp_...",
    "DIGITAL_OCEAN_KEY": "dop_v1_..."
  }
}
```

Update config:
```bash
openclaw gateway config.patch --raw '{"env":{"YOUR_KEY":"value"}}'
```

---

## Tips & Best Practices

1. **Use environment variables** for tokens (never commit them!)
2. **Test locally** before deploying
3. **Enable deploy-on-push** for automatic deployments
4. **Monitor costs** - both platforms have free tiers but can scale
5. **Use `.gitignore`** to exclude sensitive files
6. **Document as you go** - future you will thank you

---

## Next Steps

- Add custom domains
- Set up CI/CD pipelines
- Add monitoring and logging
- Implement API authentication
- Scale based on traffic

---

**Made with 🦅 by Clawd & Neo**

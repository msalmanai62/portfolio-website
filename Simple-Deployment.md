# 🚀 Simple Deployment Guide

## Deploy Muhammad Salman's Portfolio to AWS Amplify with Custom Domain

This guide will walk you through deploying your portfolio website to AWS Amplify with a custom domain (https://salman.codingtron.com) using Cloudflare for DNS management.

---

## 📋 Prerequisites

Before starting, ensure you have:

- ✅ AWS Account with appropriate permissions
- ✅ GitHub account (for repository hosting)
- ✅ Cloudflare account with domain management access
- ✅ Domain `codingtron.com` managed through Cloudflare
- ✅ Portfolio files ready for deployment
- ✅ Git installed on your local machine

---

## 📂 Step 1: Prepare GitHub Repository

### 1.1 Create GitHub Repository
1. **Login to GitHub** → Click "New repository"
2. **Repository Configuration:**
   ```
   Repository name: salman-portfolio
   Description: Muhammad Salman's Professional Portfolio
   Visibility: Public (recommended for Amplify)
   Initialize with README: No (we have our own)
   ```
3. **Click "Create repository"**

### 1.2 Push Portfolio to GitHub
```bash
# Navigate to your portfolio directory
cd /path/to/portfolio

# Initialize git repository
git init

# Add all files
git add .

# Commit files
git commit -m "Initial portfolio commit"

# Add remote origin
git remote add origin https://github.com/YOUR_USERNAME/salman-portfolio.git

# Push to GitHub
git branch -M main
git push -u origin main
```

---

## 🚀 Step 2: Deploy with AWS Amplify

### 2.1 Create Amplify App
1. **Login to AWS Console** → Navigate to AWS Amplify
2. **Click "Create new app"** → **Host web app**
3. **Choose GitHub** as your repository service
4. **Authorize AWS Amplify** to access your GitHub account
5. **Select Repository:**
   ```
   Repository: salman-portfolio
   Branch: main
   ```
6. **Click "Next"**

### 2.2 Configure Build Settings
1. **App name:** `salman-portfolio`
2. **Environment name:** `production`
3. **Build settings** (Auto-detected):
   ```yaml
   version: 1
   frontend:
     phases:
       build:
         commands:
           - echo "No build process required for static site"
     artifacts:
       baseDirectory: /
       files:
         - '**/*'
   ```
4. **Click "Next"** → **Save and deploy**

### 2.3 Wait for Deployment
- **Provision:** ~2 minutes
- **Build:** ~1 minute  
- **Deploy:** ~1 minute
- **Verify:** Automatic testing

Your app will be available at: `https://main.XXXXXXXXXX.amplifyapp.com`

---

## 🌐 Step 3: Add Custom Domain

### 3.1 Add Domain in Amplify
1. **In your Amplify app** → Go to **Domain management**
2. **Click "Add domain"**
3. **Enter domain:** `codingtron.com`
4. **Configure subdomains:**
   ```
   salman.codingtron.com → main (branch)
   www.salman.codingtron.com → main (branch) [optional]
   ```
5. **Click "Configure domain"**

### 3.2 SSL Certificate Creation
- **Amplify automatically creates** SSL certificate via AWS Certificate Manager
- **DNS validation required** - we'll configure this in Cloudflare
- **Wait for certificate** to be issued (~5-10 minutes)

---

## ☁️ Step 4: Configure Cloudflare DNS

### 4.1 Add DNS Records
1. **Login to Cloudflare Dashboard**
2. **Select your domain** `codingtron.com`
3. **Go to DNS** → **Records**
4. **Add CNAME record:**
   ```
   Type: CNAME
   Name: salman
   Target: [your-amplify-domain].amplifyapp.com
   Proxy status: DNS only (gray cloud) - IMPORTANT!
   TTL: Auto
   ```

### 4.2 SSL Certificate Validation
1. **In AWS Amplify** → Check domain status
2. **Copy DNS validation records** from Amplify
3. **Add validation records** in Cloudflare DNS:
   ```
   Type: CNAME
   Name: [validation-name]
   Target: [validation-target]
   Proxy status: DNS only (gray cloud)
   TTL: Auto
   ```

### 4.3 SSL/TLS Configuration (After Domain Verification)
1. **Go to SSL/TLS** → **Overview**
2. **Set encryption mode:** Full (strict)
3. **Go to Edge Certificates**
4. **Enable:**
   ```
   ✅ Always Use HTTPS
   ✅ HTTP Strict Transport Security (HSTS)
   ✅ Minimum TLS Version: 1.2
   ✅ Opportunistic Encryption
   ✅ TLS 1.3
   ```

---

## 🔧 Step 5: Configure Amplify Settings

### 5.1 Build Settings Optimization
1. **Go to Build settings** in Amplify
2. **Update amplify.yml:**
   ```yaml
   version: 1
   frontend:
     phases:
       preBuild:
         commands:
           - echo "No pre-build required"
       build:
         commands:
           - echo "Building static portfolio site"
       postBuild:
         commands:
           - echo "Post-build optimization"
     artifacts:
       baseDirectory: /
       files:
         - '**/*'
     cache:
       paths: []
   ```

### 5.2 Environment Variables (Optional)
```
NODE_ENV=production
AMPLIFY_DIFF_DEPLOY=false
AMPLIFY_MONOREPO_APP_ROOT=portfolio
```

### 5.3 Redirects and Rewrites
1. **Go to Rewrites and redirects**
2. **Add rules:**
   ```
   Source: /images/<*>
   Target: /images/<*>
   Type: 200 (Rewrite)
   
   Source: /<*>
   Target: /index.html
   Type: 200 (Rewrite)
   Condition: <*> (catch all)
   ```

---

## 🚀 Step 6: Automated Deployment

### 6.1 Continuous Deployment
**Amplify automatically deploys when you push to GitHub:**

```bash
# Make changes to your portfolio
git add .
git commit -m "Update portfolio content"
git push origin main

# Amplify will automatically:
# 1. Detect the push
# 2. Start a new build
# 3. Deploy the changes
# 4. Update the live site
```

### 6.2 Manual Deployment Trigger
1. **In Amplify Console** → **App overview**
2. **Click "Run build"** for manual deployment
3. **Monitor build progress** in real-time

### 6.3 Branch-based Deployments
```bash
# Create feature branch
git checkout -b feature/new-design
git push origin feature/new-design

# Amplify can auto-deploy feature branches
# Access at: https://feature-new-design.XXXXXXXXXX.amplifyapp.com
```

---

## 🔍 Step 7: Verification & Testing

### 7.1 Test Website Access
1. **Amplify Default URL:** `https://main.XXXXXXXXXX.amplifyapp.com`
2. **Custom Domain:** `https://salman.codingtron.com`
3. **Test all pages and functionality**

### 7.2 Performance Testing
Use these tools to verify performance:
- **Google PageSpeed Insights:** https://pagespeed.web.dev/
- **GTmetrix:** https://gtmetrix.com/
- **WebPageTest:** https://www.webpagetest.org/
- **Amplify Console:** Built-in performance metrics

### 7.3 SSL Certificate Verification
- **SSL Labs:** https://www.ssllabs.com/ssltest/
- **Check certificate chain and security grade**
- **Verify HTTPS redirect is working**

---

## 🛠️ Step 8: Maintenance & Updates

### 8.1 Automatic Updates via Git
```bash
# Make changes to your portfolio
git add .
git commit -m "Update portfolio content"
git push origin main

# Amplify automatically deploys changes
# No manual deployment needed!
```

### 8.2 Monitoring & Analytics
1. **Amplify Console:** Built-in monitoring and logs
2. **CloudWatch:** Detailed metrics and alarms
3. **Cloudflare Analytics:** Traffic and performance insights
4. **AWS Cost Explorer:** Monitor hosting costs

### 8.3 Build History & Rollbacks
```bash
# View build history in Amplify Console
# One-click rollback to previous versions
# Branch-based deployments for testing
```

---

## 💰 Cost Estimation

### AWS Amplify Costs (Monthly)
- **Build minutes:** Free tier: 1,000 minutes/month
- **Hosting:** Free tier: 15 GB served/month
- **Storage:** Free tier: 5 GB/month
- **SSL Certificate:** Free (AWS Certificate Manager)

### Cloudflare Costs
- **Free Plan:** $0 (sufficient for most portfolios)
- **Pro Plan:** $20/month (advanced features)

### Estimated Costs:
- **Free Tier:** $0/month (first year)
- **After Free Tier:** $1-3/month (low traffic)
- **High Traffic:** $5-15/month

**Total Estimated Cost:** $0-15/month

---

## 🔧 Troubleshooting

### Common Issues & Solutions

#### 1. **Build Failures**
```bash
# Check build logs in Amplify Console
# Common issues:
# - Missing files in repository
# - Incorrect build settings
# - Environment variable issues
```

#### 2. **Domain Verification Issues**
- Ensure DNS records are set to "DNS only" (gray cloud) in Cloudflare
- Wait for DNS propagation (up to 48 hours)
- Check AWS Certificate Manager for validation status

#### 3. **SSL Certificate Problems**
- Verify DNS validation records in Cloudflare
- Ensure CNAME records are not proxied during validation
- Check certificate status in AWS Certificate Manager

#### 4. **Custom Domain Not Working**
```bash
# Check DNS resolution
dig salman.codingtron.com

# Verify Amplify domain configuration
# Ensure Cloudflare proxy is disabled for validation
```

#### 5. **Build Performance Issues**
- Optimize build settings in amplify.yml
- Use build caching for faster deployments
- Consider excluding unnecessary files

---

## 📞 Support Resources

- **AWS Amplify Documentation:** https://docs.aws.amazon.com/amplify/
- **GitHub Support:** https://support.github.com/
- **Cloudflare Support:** https://support.cloudflare.com/
- **AWS Support:** https://aws.amazon.com/support/

---

## ✅ Deployment Checklist

- [ ] GitHub repository created and configured
- [ ] Portfolio files pushed to GitHub
- [ ] AWS Amplify app created and connected
- [ ] Initial deployment successful
- [ ] Custom domain added in Amplify
- [ ] DNS records configured in Cloudflare
- [ ] SSL certificate validated and active
- [ ] HTTPS redirect enabled
- [ ] Build settings optimized
- [ ] Website tested and verified
- [ ] Continuous deployment working
- [ ] Monitoring and analytics set up

---

## 🎉 Congratulations!

Your portfolio is now live at **https://salman.codingtron.com**

**Features Enabled:**
- ✅ Continuous deployment from GitHub
- ✅ Global CDN delivery via AWS CloudFront
- ✅ SSL/HTTPS encryption with auto-renewal
- ✅ Custom domain with professional URL
- ✅ Automatic builds on code changes
- ✅ Branch-based deployments for testing
- ✅ Built-in monitoring and analytics
- ✅ One-click rollbacks and version control
- ✅ Scalable and cost-effective hosting
- ✅ Zero-downtime deployments

---

*Last Updated: January 2026*
*Deployment Guide Version: 1.0*

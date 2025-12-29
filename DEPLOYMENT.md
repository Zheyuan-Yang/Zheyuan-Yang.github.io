# Deployment Guide for Zheyuan Yang's Blog

This guide explains how to deploy this Hexo-powered blog to GitHub Pages.

## Table of Contents
- [Prerequisites](#prerequisites)
- [Automated Deployment (Recommended)](#automated-deployment-recommended)
- [Manual Deployment](#manual-deployment)
- [Local Development](#local-development)
- [Troubleshooting](#troubleshooting)

## Prerequisites

Before deploying, ensure you have:

1. **Node.js** (v16 or higher) installed on your system
2. **Git** installed and configured
3. A **GitHub account** with access to this repository
4. **npm** (comes with Node.js)

To verify your setup:
```bash
node --version  # Should be v16 or higher
npm --version
git --version
```

## Automated Deployment (Recommended)

This repository uses **GitHub Actions** for automatic deployment. The workflow is already configured in `.github/workflows/deploy.yml`.

### How It Works

1. When you push changes to the `dev` branch, GitHub Actions automatically:
   - Installs dependencies
   - Builds the Hexo site
   - Deploys the generated files to GitHub Pages

2. Your site will be available at: `https://zheyuan-yang.github.io/`

### Step-by-Step: Deploying via GitHub Actions

1. **Make your changes locally** (add posts, modify config, etc.)

2. **Commit and push to the dev branch:**
   ```bash
   git add .
   git commit -m "Your commit message"
   git push origin dev
   ```

3. **Monitor the deployment:**
   - Go to the [Actions tab](https://github.com/Zheyuan-Yang/Zheyuan-Yang.github.io/actions) in your GitHub repository
   - Watch the workflow run to completion
   - If it succeeds ✓, your changes are live!
   - If it fails ✗, click on the workflow to see error details

4. **View your live site:**
   - Visit `https://zheyuan-yang.github.io/`
   - Changes may take 1-2 minutes to appear

### Configuring GitHub Pages

Ensure GitHub Pages is configured correctly:

1. Go to **Settings** → **Pages** in your GitHub repository
2. Under **Source**, select:
   - **Branch:** `gh-pages` (created automatically by the workflow)
   - **Folder:** `/ (root)`
3. Click **Save**

## Manual Deployment

If you prefer to deploy manually or the automated workflow isn't working:

### Method 1: Using Hexo Deploy Command

1. **Install dependencies:**
   ```bash
   npm install
   ```

2. **Clean previous builds:**
   ```bash
   npm run clean
   ```

3. **Generate static files:**
   ```bash
   npm run build
   ```

4. **Deploy to GitHub:**
   ```bash
   npm run deploy
   ```

   Note: This uses `hexo-deployer-git` which is already configured in `_config.yml`

### Method 2: Manual Push to gh-pages Branch

1. **Generate static files:**
   ```bash
   npm run build
   ```

2. **Navigate to the public folder:**
   ```bash
   cd public
   ```

3. **Initialize git (if not already):**
   ```bash
   git init
   git checkout -b gh-pages
   ```

4. **Commit and push:**
   ```bash
   git add .
   git commit -m "Deploy site"
   git remote add origin https://github.com/Zheyuan-Yang/Zheyuan-Yang.github.io.git
   git push -f origin gh-pages
   ```

## Local Development

To preview your blog locally before deploying:

1. **Install dependencies:**
   ```bash
   npm install
   ```

2. **Start the local server:**
   ```bash
   npm run server
   ```
   Or:
   ```bash
   hexo server
   ```

3. **View your blog:**
   - Open your browser and go to: `http://localhost:4000`
   - The server will auto-reload when you make changes

4. **Create new content:**
   ```bash
   # Create a new post
   hexo new post "My New Post Title"
   
   # Create a new page
   hexo new page "page-name"
   ```

## Troubleshooting

### Deployment Workflow Fails

**Problem:** GitHub Actions workflow fails to deploy

**Solutions:**
1. Check the Actions tab for error messages
2. Ensure `GITHUB_TOKEN` permissions are enabled:
   - Go to **Settings** → **Actions** → **General**
   - Under "Workflow permissions", select "Read and write permissions"
   - Click **Save**
3. Verify the workflow file syntax in `.github/workflows/deploy.yml`

### Site Not Updating

**Problem:** Changes don't appear on the live site

**Solutions:**
1. Clear your browser cache (Ctrl+F5 or Cmd+Shift+R)
2. Wait 2-5 minutes for GitHub Pages to update
3. Check if the deployment workflow completed successfully
4. Verify you pushed to the correct branch (`dev`)

### Build Errors

**Problem:** `npm run build` fails locally

**Solutions:**
1. Delete `node_modules` and reinstall:
   ```bash
   rm -rf node_modules
   npm install
   ```
2. Clear Hexo cache:
   ```bash
   npm run clean
   npm run build
   ```
3. Check for syntax errors in your markdown files or `_config.yml`

### URL Configuration Issues

**Problem:** Links or resources not loading correctly

**Solution:** Update the `url` field in `_config.yml`:
```yaml
url: https://zheyuan-yang.github.io
```

### Theme Not Displaying Correctly

**Problem:** Theme files missing or not rendering

**Solutions:**
1. Ensure theme is specified in `_config.yml`:
   ```yaml
   theme: cube
   ```
2. Verify theme files exist in `themes/cube/`
3. Reinstall theme dependencies if needed

## Additional Resources

- [Hexo Documentation](https://hexo.io/docs/)
- [GitHub Pages Documentation](https://docs.github.com/en/pages)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Hexo Commands Reference](https://hexo.io/docs/commands)

## Quick Reference

| Action | Command |
|--------|---------|
| Install dependencies | `npm install` |
| Create new post | `hexo new post "title"` |
| Start local server | `npm run server` |
| Clean build files | `npm run clean` |
| Generate static files | `npm run build` |
| Deploy manually | `npm run deploy` |

---

**Need Help?** If you encounter issues not covered in the troubleshooting section:
1. Check the [GitHub Actions workflow runs](https://github.com/Zheyuan-Yang/Zheyuan-Yang.github.io/actions) for error details
2. [Open an issue](https://github.com/Zheyuan-Yang/Zheyuan-Yang.github.io/issues/new) with:
   - Description of the problem
   - Steps to reproduce
   - Error messages or screenshots
   - Your environment (OS, Node.js version, etc.)

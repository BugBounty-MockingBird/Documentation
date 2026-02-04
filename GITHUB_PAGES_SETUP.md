# GitHub Pages Setup Instructions

This document provides instructions for the repository administrator to enable GitHub Pages.

## 📋 Prerequisites

- Repository administrator access
- All documentation files committed to the `main` branch in `/docs` folder

## ⚙️ Enable GitHub Pages

### Step 1: Access Repository Settings

1. Navigate to your GitHub repository: `https://github.com/BugBounty-MockingBird/Documentation`
2. Click on **Settings** (gear icon in the top menu)

### Step 2: Configure GitHub Pages

1. In the left sidebar, scroll down and click on **Pages** under "Code and automation"
2. Under **Build and deployment**:
   - **Source**: Select "Deploy from a branch"
   - **Branch**: Select `main` branch
   - **Folder**: Select `/docs`
   - Click **Save**

### Step 3: Wait for Deployment

1. GitHub will automatically build and deploy your site
2. This process usually takes 1-2 minutes
3. A green checkmark will appear when deployment is complete

### Step 4: Verify Deployment

1. Your documentation will be available at:
   - Default URL: `https://bugbounty-mockingbird.github.io/Documentation/`
   - Or with custom domain (if configured): `https://docs.bugbounty-ksp.com`

2. Click the URL shown in the Pages settings to view your live documentation

## 🌐 Optional: Configure Custom Domain

If you want to use a custom domain like `docs.bugbounty-ksp.com`:

### Step 1: DNS Configuration

Add a CNAME record in your DNS provider:

```
Type: CNAME
Name: docs (or your subdomain)
Value: bugbounty-mockingbird.github.io
TTL: 3600 (or default)
```

### Step 2: Add Custom Domain in GitHub

1. In the Pages settings, under **Custom domain**
2. Enter your domain: `docs.bugbounty-ksp.com`
3. Click **Save**
4. Wait for DNS check to complete (can take a few minutes)

### Step 3: Enable HTTPS

1. Once DNS is verified, check the box:
   - ✅ **Enforce HTTPS**
2. GitHub will automatically provision an SSL certificate

## 🔍 Verify Setup

After enabling GitHub Pages:

1. Visit your documentation site
2. Check that all pages load correctly
3. Verify navigation links work
4. Test all internal links between documentation pages

## 🎨 Theme Configuration

The documentation uses the Cayman theme. To change it:

1. Edit `/docs/_config.yml`
2. Change the `theme` line to one of:
   - `jekyll-theme-cayman`
   - `jekyll-theme-minimal`
   - `jekyll-theme-modernist`
   - `jekyll-theme-slate`
   - `jekyll-theme-tactile`
   - `jekyll-theme-time-machine`

## 🐛 Troubleshooting

### Pages Not Loading

- Verify the branch and folder are correctly set in Pages settings
- Check that all markdown files have proper frontmatter (if needed)
- Look for build errors in the Actions tab

### 404 Errors

- Ensure all links in markdown files are relative (e.g., `getting-started.md`, not `/getting-started.md`)
- Check that file names match the links exactly (case-sensitive)

### Theme Not Applied

- Make sure `_config.yml` is in the `/docs` folder
- Verify the theme name is spelled correctly
- Wait a few minutes for GitHub to rebuild the site

## 📝 Updating Documentation

After the initial setup, any changes pushed to the `/docs` folder in the `main` branch will automatically trigger a rebuild and deployment.

To update documentation:

```bash
# Make changes to files in docs/
git add docs/
git commit -m "docs: update getting started guide"
git push origin main

# Wait 1-2 minutes for GitHub to rebuild
# Changes will be live at your GitHub Pages URL
```

## 🔗 Useful Links

- [GitHub Pages Documentation](https://docs.github.com/en/pages)
- [Jekyll Themes](https://pages.github.com/themes/)
- [Markdown Guide](https://www.markdownguide.org/)

## ✅ Checklist

Before marking the setup complete, verify:

- [ ] GitHub Pages is enabled in repository settings
- [ ] Source is set to `main` branch, `/docs` folder
- [ ] Documentation site is accessible via GitHub Pages URL
- [ ] All 9 documentation sections are visible and accessible
- [ ] Navigation links work correctly
- [ ] Theme is applied correctly
- [ ] (Optional) Custom domain is configured and working
- [ ] (Optional) HTTPS is enforced

---

Once completed, the documentation will be available at your GitHub Pages URL!

# Deployment Guide for Personal Site

Your personal site is split into two repositories:
- **jagankumar-content** - JSON data only
- **jagankumar-site** - Astro static site that renders the JSON

## Quick Start

### Step 1: Push Both Repos to GitHub

```bash
cd /Users/jagankumar/Office/Work/repo/ejagankumar/jagankumar-content
gh repo create ejagankumar/jagankumar-content --public --source=. --remote=origin --push

cd /Users/jagankumar/Office/Work/repo/ejagankumar/jagankumar-site
gh repo create ejagankumar/jagankumar-site --public --source=. --remote=origin --push
```

Or manually via GitHub web interface:
1. Create two new repositories: `jagankumar-content` and `jagankumar-site`
2. Push each repo:
   ```bash
   cd jagankumar-content
   git remote add origin git@github.com:ejagankumar/jagankumar-content.git
   git push -u origin main

   cd ../jagankumar-site
   git remote add origin git@github.com:ejagankumar/jagankumar-site.git
   git push -u origin main
   ```

### Step 2: Enable GitHub Pages for Site Repo

1. Go to https://github.com/ejagankumar/jagankumar-site
2. Click **Settings → Pages**
3. Under "Source", select **GitHub Actions**
4. Save

### Step 3: Set Environment Variables for Site Repo

1. Go to https://github.com/ejagankumar/jagankumar-site
2. Click **Settings → Secrets and variables → Actions**
3. Click the **Variables** tab
4. Add these two variables:

| Name | Value |
|------|-------|
| `CONTENT_REPO_RAW_BASE` | `https://raw.githubusercontent.com/ejagankumar/jagankumar-content/main` |
| `SITE_URL` | `https://ejagankumar.github.io` |

### Step 4: Set Up Auto-Rebuild (Content → Site)

This makes the site rebuild automatically when you edit JSON in the content repo.

1. **Generate a Personal Access Token:**
   - Go to https://github.com/settings/tokens
   - Click **Tokens (classic)** → **Generate new token (classic)**
   - Give it a name like "Content to Site Dispatch"
   - Select scope: **repo** (all checkboxes under repo)
   - Click **Generate token**
   - **Copy the token** (you won't see it again!)

2. **Add token as secret in content repo:**
   - Go to https://github.com/ejagankumar/jagankumar-content
   - Click **Settings → Secrets and variables → Actions → New repository secret**
   - Name: `SITE_REPO_DISPATCH_TOKEN`
   - Value: paste the token you copied
   - Click **Add secret**

### Step 5: Trigger First Deployment

1. Go to https://github.com/ejagankumar/jagankumar-site/actions
2. Click on "Build and deploy site" workflow
3. Click **Run workflow** → **Run workflow**
4. Wait ~2 minutes for it to complete

Your site will be live at: **https://ejagankumar.github.io**

## How It Works

1. **Edit content** - Make changes to JSON files in `jagankumar-content/data/`
2. **Commit & push** - Push to main branch
3. **Auto-rebuild** - Content repo workflow triggers site repo to rebuild
4. **Deploy** - Site rebuilds with new content and deploys to GitHub Pages

## Updating Content

```bash
cd /Users/jagankumar/Office/Work/repo/ejagankumar/jagankumar-content

# Edit any JSON file
vim data/profile.json

# Commit and push
git add -A
git commit -m "Update profile information"
git push

# Site automatically rebuilds within 2-3 minutes
```

## Local Development

To test the site locally:

```bash
cd /Users/jagankumar/Office/Work/repo/ejagankumar/jagankumar-site

# Copy environment example
cp .env.example .env

# Install dependencies
npm install

# Start dev server
npm run dev

# Open http://localhost:4321
```

## Testing Before Going Live

Before deploying, you can verify the content locally:

```bash
cd jagankumar-site
npm install
npm run dev
```

Visit http://localhost:4321 to preview the site.

## Custom Domain (Optional)

To use a custom domain like `jagankumar.dev`:

1. **Buy the domain** (Namecheap, Cloudflare, etc.)

2. **Add CNAME file** to `jagankumar-site/public/`:
   ```bash
   echo "jagankumar.dev" > jagankumar-site/public/CNAME
   git add public/CNAME
   git commit -m "Add custom domain"
   git push
   ```

3. **Update SITE_URL variable** in GitHub:
   - Go to repo Settings → Actions → Variables
   - Edit `SITE_URL` to `https://jagankumar.dev`

4. **Configure DNS** at your domain registrar:
   - Add a `CNAME` record:
     - Name: `@` (or leave blank)
     - Value: `ejagankumar.github.io`
   - Or for subdomain (www):
     - Name: `www`
     - Value: `ejagankumar.github.io`

5. **Enable in GitHub**:
   - Go to repo Settings → Pages
   - Under "Custom domain", enter your domain
   - Check "Enforce HTTPS" (wait ~24 hours for certificate)

6. **Update seo.json**:
   ```bash
   # Edit jagankumar-content/data/seo.json
   # Change siteUrl to your custom domain
   git commit -am "Update to custom domain"
   git push
   ```

## Monitoring Deployments

- **Site deployments**: https://github.com/ejagankumar/jagankumar-site/actions
- **Content triggers**: https://github.com/ejagankumar/jagankumar-content/actions

## Troubleshooting

### Site not updating after content change

1. Check content repo workflow ran: https://github.com/ejagankumar/jagankumar-content/actions
2. Check site repo received trigger: https://github.com/ejagankumar/jagankumar-site/actions
3. Verify `SITE_REPO_DISPATCH_TOKEN` secret is set correctly

### Build failing

1. Check Actions tab for error logs
2. Verify both environment variables are set in site repo
3. Try manual workflow dispatch to see detailed error

### Local dev not working

```bash
cd jagankumar-site
rm -rf node_modules package-lock.json
npm install
npm run dev
```

## SEO Optimization

Your site is already optimized for search:

- ✅ Static HTML (fully crawlable)
- ✅ Per-project pages (individually indexable)
- ✅ JSON-LD Person schema (for Google knowledge panel)
- ✅ Sitemap auto-generated
- ✅ Meta tags & Open Graph
- ✅ robots.txt configured

### Submit to Google

Once live:

1. Go to [Google Search Console](https://search.google.com/search-console)
2. Add your site (https://ejagankumar.github.io)
3. Verify ownership (via GitHub Pages is easiest)
4. Submit sitemap: `https://ejagankumar.github.io/sitemap-index.xml`

Keywords that should rank:
- Your name: "Jagankumar E", "Jagankumar egov"
- Projects: "CARE DICOM Enabler", "eAushadhi integration"
- Tech: "CARE HMIS", "DIGIT PGR", "OHIF Viewer DCM4CHEE"

## Repository Structure

```
jagankumar-content/
├── .github/workflows/
│   └── notify-site.yml          # Triggers site rebuild on push
├── data/
│   ├── profile.json             # Name, bio, contact
│   ├── experience.json          # Work history
│   ├── projects.json            # Project case studies
│   ├── skills.json              # Tech skills grouped
│   └── seo.json                 # Meta tags, keywords
└── README.md

jagankumar-site/
├── .github/workflows/
│   └── deploy.yml               # Builds and deploys to GitHub Pages
├── public/
│   ├── favicon.svg              # Site icon
│   └── robots.txt               # Search engine instructions
├── src/
│   ├── layouts/
│   │   └── Base.astro           # Base layout with SEO
│   ├── lib/
│   │   └── content.js           # Fetches JSON at build time
│   └── pages/
│       ├── index.astro          # Homepage
│       └── projects/[slug].astro # Dynamic project pages
├── astro.config.mjs             # Astro configuration
├── package.json                 # Dependencies
└── README.md
```

## Next Steps

1. **Deploy now** using the steps above
2. **Test the site** at https://ejagankumar.github.io
3. **Update content** by editing JSON files and pushing
4. **Monitor rankings** - search "Jagankumar egov" in a few weeks
5. **Consider custom domain** if you want professional branding

Your site is production-ready and SEO-optimized. Deploy whenever you're ready!

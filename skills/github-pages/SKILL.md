---
name: github-pages
description: Manage GitHub Pages sites using the gh CLI. Create, configure, monitor, and deploy Pages sites.
---

# GitHub Pages Management Skill

You manage GitHub Pages sites using `gh api` and git operations. You help users create, configure, monitor, and deploy GitHub Pages.

## Available Operations

When the user invokes this skill, determine which operation they need and execute it. If unclear, ask.

### 1. Status — Check a Pages site

```bash
gh api repos/{owner}/{repo}/pages
```

Report: URL, status, source (branch/path), custom domain, HTTPS enforcement, build type.

### 2. Create — Enable Pages on a repository

Two approaches depending on desired publishing source:

**Branch-based publishing:**
```bash
gh api repos/{owner}/{repo}/pages -X POST \
  -f source[branch]=main \
  -f source[path]=/
```

**GitHub Actions publishing:**
Generate a workflow file at `.github/workflows/pages.yml` and push it. Then enable Pages via Settings or API.

> Note: `GITHUB_TOKEN` in Actions cannot create/delete Pages sites (returns 403). If the API call fails, instruct the user to either use a PAT or enable Pages manually in repo Settings > Pages.

### 3. Configure — Update Pages settings

**Change source branch/path:**
```bash
gh api repos/{owner}/{repo}/pages -X PUT \
  -f source[branch]=gh-pages \
  -f source[path]=/docs
```

**Set custom domain:**
```bash
gh api repos/{owner}/{repo}/pages -X PUT -f cname=example.com
```
Also create a `CNAME` file in the repo root containing the domain name.

**Remove custom domain:**
```bash
gh api repos/{owner}/{repo}/pages -X PUT -f cname=
```

### 4. DNS Health — Check custom domain DNS

```bash
gh api repos/{owner}/{repo}/pages/health
```

Report any DNS misconfigurations or propagation issues.

### 5. Builds — List and trigger builds

**List recent builds:**
```bash
gh api repos/{owner}/{repo}/pages/builds
```

**Get latest build:**
```bash
gh api repos/{owner}/{repo}/pages/builds/latest
```

**Trigger a new build:**
```bash
gh api repos/{owner}/{repo}/pages/builds -X POST
```

Report: build status, created_at, duration, error messages if any.

### 6. Deploy — Push content to Pages

For branch-based sites, commit and push content to the configured source branch. For Actions-based sites, trigger the workflow:

```bash
gh workflow run pages.yml
```

### 7. Workflow — Generate a GitHub Actions deployment workflow

Generate `.github/workflows/pages.yml` appropriate to the project's static site generator. Ask the user which generator they use if not obvious from the repo contents.

**Static HTML (no build):**
```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: ["main"]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/configure-pages@v5
      - uses: actions/upload-pages-artifact@v3
        with:
          path: '.'
      - id: deployment
        uses: actions/deploy-pages@v4
```

**With a build step** (e.g., Node.js / Vite / Next.js):
```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: ["main"]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: npm
      - run: npm ci
      - run: npm run build
      - uses: actions/configure-pages@v5
      - uses: actions/upload-pages-artifact@v3
        with:
          path: './dist'

  deploy:
    needs: build
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - id: deployment
        uses: actions/deploy-pages@v4
```

Adapt the build commands and output path to match the specific generator.

### 8. Disable — Remove Pages from a repository

```bash
gh api repos/{owner}/{repo}/pages -X DELETE
```

> Note: This also requires elevated permissions (PAT with `repo` scope). `GITHUB_TOKEN` cannot delete Pages sites.

## Resolving {owner}/{repo}

When the user doesn't specify a repo, detect it from the current git remote:

```bash
gh repo view --json nameWithOwner -q .nameWithOwner
```

## Key Constraints

- **Site size limit**: 1 GB
- **Bandwidth**: 100 GB/month (soft)
- **Builds**: 10/hour for branch-based (Actions-based exempt)
- **Deployment timeout**: 10 minutes
- **Artifact limit**: 10 GB
- Non-Jekyll sites need a `.nojekyll` file in the root to prevent Jekyll processing
- Project sites (non-`*.github.io` repos) serve at `/<repo-name>/` — generators need `base`/`baseurl` configured accordingly

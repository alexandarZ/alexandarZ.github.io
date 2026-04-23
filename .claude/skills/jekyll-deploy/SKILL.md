# Jekyll Deploy Skill

Configure GitHub Actions for Pages deployment — generates the correct `.github/workflows/jekyll.yml` for both classic Pages and the newer `actions/jekyll-build-pages`.

## Steps

1. **Detect deployment method preference.** Ask the user (or infer from `_config.yml` / existing workflow files):
   - **Classic GitHub Pages** — push to `gh-pages` branch or build from `main`/`master` root/`docs/`. Simple but locked to GitHub Pages gem versions.
   - **GitHub Actions (recommended)** — full control over Jekyll version, plugins, and build environment.

2. **Check for existing workflows** in `.github/workflows/`. If one exists, read it before making changes.

3. **Detect Ruby/Jekyll version** from `Gemfile` or `.ruby-version`. Default to the latest stable if not found.

### Option A — GitHub Actions with `actions/jekyll-build-pages` (simplest)

Creates `.github/workflows/jekyll.yml`:

```yaml
name: Deploy Jekyll site to Pages

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
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Pages
        uses: actions/configure-pages@v5

      - name: Build with Jekyll
        uses: actions/jekyll-build-pages@v1
        with:
          source: ./
          destination: ./_site

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

### Option B — GitHub Actions with full Ruby/Bundler control

Creates `.github/workflows/jekyll.yml`:

```yaml
name: Deploy Jekyll site to Pages

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
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Ruby
        uses: ruby/setup-ruby@v1
        with:
          ruby-version: "3.3"
          bundler-cache: true

      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5

      - name: Build with Jekyll
        run: bundle exec jekyll build --baseurl "${{ steps.pages.outputs.base_path }}"
        env:
          JEKYLL_ENV: production

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

4. **Ensure a `Gemfile` exists.** If missing, scaffold a minimal one:

```ruby
source "https://rubygems.org"

gem "jekyll", "~> 4.3"
gem "minima", "~> 2.5"

group :jekyll_plugins do
  gem "jekyll-feed"
  gem "jekyll-seo-tag"
  gem "jekyll-sitemap"
end
```

5. **Check GitHub Pages source setting** — remind the user to go to **Settings → Pages → Source** and set it to **GitHub Actions** (not a branch) so the workflow deployment is used.

6. **Report back** with the workflow file path, the chosen option, and next steps (push to trigger first build, check the Actions tab for status).

## Edge cases

- If the repo uses a custom domain, remind the user to add a `CNAME` file at the root with just the domain name.
- `baseurl` must be `""` for user/org pages (`username.github.io`) and `"/repo-name"` for project pages. The `configure-pages` action injects this automatically — do not hardcode it.
- If the user has non-whitelisted plugins, Option A will fail silently. Option B (full Bundler) is required.

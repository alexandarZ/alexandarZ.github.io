# Jekyll Audit Skill

Review the whole site for common issues: broken internal links, missing alt text, posts with no layout, `_config.yml` misconfigs, and large uncompressed assets.

## Steps

Run all checks below and compile a single report grouped by severity: **Error** (breaks the build or site), **Warning** (degrades quality), **Info** (suggestions).

---

### 1. `_config.yml` Health Check

Read `_config.yml` and verify:

- [ ] `url` is set and matches the live domain (no trailing slash).
- [ ] `baseurl` is correct (`""` for user pages, `"/repo"` for project pages).
- [ ] `title` and `description` are non-empty.
- [ ] `plugins` list matches gems declared in `Gemfile`.
- [ ] No duplicate keys (YAML silently uses the last value for duplicates).
- [ ] `exclude` list includes `Gemfile`, `Gemfile.lock`, `vendor/`, `node_modules/`, `README.md`.
- [ ] `markdown: kramdown` (default for GitHub Pages) or explicitly set.
- [ ] `safe: true` not set unless intentional (blocks most plugins).

---

### 2. Post & Page Frontmatter Audit

Scan all files in `_posts/`, `_pages/`, and the root (`.md`, `.html`):

- [ ] Every post has `layout` defined (or a matching default in `_config.yml`).
- [ ] Every post has `title` (non-empty).
- [ ] Every post has `date` in `YYYY-MM-DD` or `YYYY-MM-DD HH:MM:SS +TZ` format.
- [ ] No post file is dated in the future unless `future: true` is set in config (would be silently excluded from build).
- [ ] Post filenames match the pattern `YYYY-MM-DD-slug.md`.
- [ ] No duplicate slugs on the same date.

---

### 3. Broken Internal Links

Search all `.md` and `.html` files for:
- Markdown links `[text](path)` where `path` starts with `/` — check that `path` resolves to an existing file or collection document.
- `href` attributes in HTML includes/layouts that reference internal paths.
- `src` attributes for local images — verify each `assets/` or `images/` path exists on disk.

Flag absolute URLs pointing to `localhost` or `127.0.0.1` (left-over from dev).

---

### 4. Missing Alt Text

Scan all `.md` files for:
- `![](image.jpg)` — image with empty alt text.
- `<img src="..." >` without an `alt` attribute.

List each occurrence with file path and line number.

---

### 5. Large or Unoptimized Assets

Scan `assets/` for:
- Images larger than **200 KB** — flag for compression (suggest `imageoptim`, `squoosh`, or the `jekyll-compress-images` plugin).
- Images not in a web-friendly format (`.bmp`, `.tiff`, `.psd`) — suggest converting to `.webp` or `.jpg`.
- JavaScript or CSS files larger than **100 KB** that are not minified.

---

### 6. Gemfile & Dependency Checks

- [ ] `Gemfile.lock` exists (important for reproducible builds).
- [ ] `jekyll-feed` is present (recommended for RSS).
- [ ] `jekyll-seo-tag` is present (recommended for SEO).
- [ ] `jekyll-sitemap` is present (recommended for SEO).
- [ ] No gems pinned to outdated major versions (check against current stable on RubyGems).

---

### 7. GitHub Actions / Deployment Check (if `.github/workflows/` exists)

- [ ] Workflow triggers on the correct branch (`main` or `master`).
- [ ] Pages source in workflow matches repo's GitHub Pages setting.
- [ ] `JEKYLL_ENV: production` is set in the build step.
- [ ] `actions/checkout@v4` (or latest) is used — flag v1/v2 as outdated.

---

### Report Format

```
## Jekyll Audit Report — YYYY-MM-DD

### Errors (must fix)
- [ERROR] _config.yml: `url` is missing
- [ERROR] _posts/2024-01-15-hello.md: no `layout` defined

### Warnings (should fix)
- [WARN] assets/img/hero.png: 1.4 MB — compress before deploying
- [WARN] about.md: image ![](/assets/img/team.jpg) has empty alt text

### Info (nice to have)
- [INFO] Gemfile: jekyll-sitemap not present — add for better SEO
- [INFO] _config.yml: consider adding `excerpt_separator` for cleaner post excerpts

### Summary
Errors: 2  |  Warnings: 3  |  Info: 2
```

After reporting, ask the user which issues they'd like fixed and address them one by one (or invoke the relevant skill, e.g. `jekyll-seo` for SEO issues).

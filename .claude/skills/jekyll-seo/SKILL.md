# Jekyll SEO Skill

Audit and inject SEO tags: `jekyll-seo-tag` config, Open Graph meta, canonical URLs, sitemap setup, and `robots.txt`.

## Steps

### 1. Audit current SEO state

Read `_config.yml`, `Gemfile`, and the default layout (`_layouts/default.html` or the theme's equivalent). Check for:

- [ ] `jekyll-seo-tag` gem and plugin present.
- [ ] `{% seo %}` tag in the `<head>`.
- [ ] `title`, `description`, `url`, `author` set in `_config.yml`.
- [ ] `jekyll-sitemap` plugin present.
- [ ] `robots.txt` at repo root.
- [ ] Open Graph / Twitter Card fields on key pages.

Report the audit results before making any changes.

### 2. Add `jekyll-seo-tag`

If missing, add to `Gemfile`:
```ruby
gem "jekyll-seo-tag"
```

Add to `_config.yml`:
```yaml
plugins:
  - jekyll-seo-tag
```

Add `{% seo %}` inside `<head>` in the default layout (if not already present). For remote themes that own the layout, create a local `_layouts/default.html` override (see `jekyll-theme` skill).

### 3. Configure core SEO fields in `_config.yml`

```yaml
title: "Site Name"
description: "One-sentence description of your site."
url: "https://username.github.io"           # no trailing slash
baseurl: ""                                  # leave empty for user pages
author:
  name: "Your Name"
  email: your@email.com
  twitter: yourtwitterhandle                 # without @
lang: en

twitter:
  username: yourtwitterhandle
  card: summary_large_image

social:
  name: "Your Name"
  links:
    - https://twitter.com/yourtwitterhandle
    - https://github.com/yourusername
    - https://www.linkedin.com/in/yourprofile

google_analytics: UA-XXXXXXXX-X             # or G-XXXXXXXXXX for GA4
```

### 4. Add per-post/page SEO frontmatter

For each important post or page, ensure:
```yaml
---
title: "Descriptive Page Title | Site Name"
description: "150-160 char meta description. Appears in search snippets."
image: /assets/img/og-cover.jpg             # 1200×630 px ideal for OG
canonical_url: https://yoursite.com/page/   # add only if needed (syndicated content)
---
```

### 5. Add `jekyll-sitemap`

Add to `Gemfile`:
```ruby
gem "jekyll-sitemap"
```

Add to `_config.yml` plugins list:
```yaml
plugins:
  - jekyll-seo-tag
  - jekyll-sitemap
```

Optionally exclude pages from the sitemap:
```yaml
exclude_from_sitemap:
  - /404.html
  - /thanks/
```

### 6. Create `robots.txt`

Create `robots.txt` at the repo root:
```
User-agent: *
Allow: /

Sitemap: https://username.github.io/sitemap.xml
```

### 7. Report results

List every file created or modified, note which audit items were fixed vs. still outstanding, and remind the user to run `bundle install` if new gems were added.

## Edge cases

- GitHub Pages runs `jekyll-seo-tag` and `jekyll-sitemap` as whitelisted plugins — no `Gemfile` changes needed if deploying via classic GitHub Pages (but still add them for local dev parity).
- If `url` in `_config.yml` is wrong, canonical URLs and sitemap URLs will be incorrect — verify this first.
- `jekyll-seo-tag` generates `og:image` from the page's `image` field **or** `site.logo`; ensure at least one is set.

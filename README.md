# YT3MW Portable Radio

Personal site and blog — live at [alexandarZ.github.io](https://alexandarZ.github.io).

Built with [Jekyll](https://jekyllrb.com) + [Minima](https://github.com/jekyll/minima), deployed via GitHub Actions.

---

## Local development

**Prerequisites:** Ruby 3.3+ via [rbenv](https://github.com/rbenv/rbenv), Bundler.

```bash
# First time setup
bundle config set --local path vendor/bundle
bundle install

# Start dev server with live reload
bundle exec jekyll serve --livereload

# Also preview draft posts
bundle exec jekyll serve --livereload --drafts
```

Site runs at `http://localhost:4000`.

> `_config.yml` changes require a server restart — live reload won't pick them up automatically.

---

## Writing content

New post:
```bash
# File: _posts/YYYY-MM-DD-your-title.md
---
layout: post
title: "Your Title"
date: 2026-04-23
categories: [General]
tags: [tag1, tag2]
excerpt: "One sentence summary."
---

Post content here.
```

---

## Deployment

Pushing to `master` triggers the GitHub Actions workflow automatically.
Monitor builds at: [github.com/alexandarZ/alexandarZ.github.io/actions](https://github.com/alexandarZ/alexandarZ.github.io/actions).

Pages source must be set to **GitHub Actions** in repo Settings → Pages.

---

## Claude Code skills

Ask Claude to run any of these by name (e.g. "run /jekyll-post"):

| Skill | What it does |
|---|---|
| `/jekyll-post` | Scaffold a new blog post with correct frontmatter |
| `/jekyll-page` | Create a standalone page and register it in nav |
| `/jekyll-component` | Build a reusable Liquid include (`_includes/`) |
| `/jekyll-theme` | Customise or override the Minima theme via `_sass/` |
| `/jekyll-collection` | Set up a new Jekyll collection (projects, talks, etc.) |
| `/jekyll-seo` | Audit and inject SEO tags, sitemap, robots.txt |
| `/jekyll-data` | Generate `_data/*.yml` files + Liquid templates |
| `/jekyll-deploy` | Regenerate the GitHub Actions deployment workflow |
| `/jekyll-migrate` | Import content from Notion, WordPress, or Markdown |
| `/jekyll-update` | Update Jekyll, gems, and GitHub Actions to latest versions |
| `/jekyll-audit` | Full site health check — broken links, SEO, config issues |

Skills live in [`.claude/skills/`](.claude/skills/).

---

## Project structure

```
.
├── _posts/          # Blog posts (YYYY-MM-DD-title.md)
├── _data/           # YAML data files (nav, team, FAQs, etc.)
├── _includes/       # Reusable Liquid components
├── _layouts/        # Page layout overrides
├── _sass/           # SCSS theme overrides
├── assets/          # CSS, JS, images
├── .claude/skills/  # Claude Code skills
├── .github/workflows/jekyll.yml  # Deploy workflow
├── _config.yml      # Site configuration
├── Gemfile          # Ruby dependencies
└── about.markdown   # About page
```
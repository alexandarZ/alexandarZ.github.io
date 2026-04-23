# Jekyll Page Skill

Create standalone Jekyll pages (e.g. `about.md`, `projects.md`) with correct frontmatter, permalink config, and nav registration in `_config.yml`.

## Steps

1. **Gather intent.** Ask the user for:
   - Page name / title (e.g. "About", "Projects", "Contact").
   - Desired URL slug / permalink (default: `/<slug>/`).
   - Whether the page should appear in the site navigation.

2. **Detect layout options** by reading `_config.yml` and scanning `_layouts/`. Use `page` as the default layout unless the site uses a custom one.

3. **Create the page file** at the repo root (or a subdirectory if the user requests it). Filename: `<slug>.md` or `<slug>/index.md` for cleaner URLs.

   Frontmatter template:

```yaml
---
layout: page
title: "Page Title"
permalink: /slug/
nav_order: 2          # used by themes like Just the Docs; remove if not applicable
nav_exclude: false    # set true to hide from auto-generated nav
description: "Short description for SEO and meta tags."
---
```

4. **Add a placeholder body** with relevant headings so the file is immediately editable.

5. **Register in navigation** — check `_config.yml` for a `nav_pages`, `header_pages`, or similar key. If present, append the new filename to the list. Example for Minima:

```yaml
header_pages:
  - about.md
  - projects.md
```

   If the theme uses a `_data/navigation.yml` (e.g. Cayman-blog, Minimal Mistakes), add an entry there instead:

```yaml
- title: "Projects"
  url: /projects/
```

6. **Report back** with the file path, live URL (based on `permalink`), and the exact line added to `_config.yml` or the data file.

## Edge cases

- If `permalink` clashes with an existing page, warn the user and suggest an alternative slug.
- If the theme is Minima and `header_pages` is not set, explain that all root-level `.md` pages appear automatically — no config change needed.
- For multilingual sites (jekyll-multiple-languages or Polyglot), note that each language will need its own copy.

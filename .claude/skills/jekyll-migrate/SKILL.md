# Jekyll Migrate Skill

Import content from another source (Notion export, WordPress XML, plain markdown files) and reformat it into Jekyll-compatible posts with correct frontmatter.

## Steps

1. **Identify the source.** Ask the user which format they're migrating from:
   - **Notion export** — Markdown + CSV zip.
   - **WordPress** — WXR XML export file.
   - **Plain Markdown files** — files with inconsistent or missing frontmatter.
   - **Ghost** — JSON export.
   - **DEV.to / Hashnode** — Markdown with their own frontmatter schema.
   - **Other** — describe the format.

2. **Locate the source files.** Ask the user for the path to the export file or directory.

---

### Notion Export

3. Unzip the export. Each `.md` file is a post candidate.
4. Notion filenames include a UUID suffix — strip it: `My Post 1a2b3c4d.md` → `my-post.md`.
5. Notion exports have no frontmatter. Derive from:
   - Title: first `# Heading` in the file.
   - Date: file modification time or a "Published" property if the database export includes a CSV.
   - Tags/Categories: from database property columns in the CSV (join with `|`).
6. Rename and move files to `_posts/YYYY-MM-DD-slug.md`.
7. Clean up Notion-specific artifacts: callout blocks (`> 💡`), toggle blocks, broken image links.

---

### WordPress XML

3. Read the WXR file. For each `<item>` where `<wp:post_type>` is `post` and `<wp:status>` is `publish`:
   - Extract: `<title>`, `<pubDate>`, `<content:encoded>`, `<category>`, `<description>`.
   - Convert `<content:encoded>` from HTML to Markdown (flag for manual review if complex shortcodes are present).
   - Build the frontmatter block.
4. Save to `_posts/YYYY-MM-DD-slug.md`. Derive slug from `<link>` or title.
5. Download embedded images referenced by absolute URLs and save to `assets/img/posts/`, then rewrite `src` attributes to relative paths.

---

### Plain Markdown Files

3. Scan all `.md` files in the source directory.
4. For each file, check whether it has a valid YAML frontmatter block (starts with `---`).
5. If frontmatter is missing or incomplete, infer:
   - `title`: first `# Heading` or filename.
   - `date`: file modification time.
   - `layout`: `post`.
6. Normalize to Jekyll conventions and move to `_posts/`.

---

### Frontmatter Template (all sources)

```yaml
---
layout: post
title: "Imported Post Title"
date: YYYY-MM-DD HH:MM:SS +0000
categories: [category]
tags: [tag1, tag2]
excerpt: "Auto-extracted first paragraph or original description."
source: notion         # track origin for auditing
migrated: true         # flag for post-migration review
---
```

6. **Generate a migration report** listing:
   - Total posts found vs. successfully converted.
   - Posts skipped (drafts, pages, unsupported formats).
   - Files that need manual review (complex shortcodes, broken images, missing dates).

## Edge cases

- If two posts produce the same date + slug, append `-2`, `-3`, etc. to the filename.
- Notion exports image links as relative paths inside the zip folder — adjust them to `/assets/img/posts/<image>` and copy the image files.
- WordPress shortcodes (`[gallery]`, `[caption]`) cannot be auto-converted — flag them with an HTML comment `<!-- TODO: replace shortcode -->`.
- Never overwrite existing `_posts/` files without asking the user.

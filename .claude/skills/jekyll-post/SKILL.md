# Jekyll Post Skill

Scaffold a new Jekyll blog post by generating a `_posts/YYYY-MM-DD-title.md` file with correct frontmatter.

## Steps

1. **Determine the filename.** Ask the user for the post title if not provided. Convert it to lowercase kebab-case. Use today's date (`currentDate` from context, or run `date +%Y-%m-%d` if unavailable). Filename format: `_posts/YYYY-MM-DD-kebab-title.md`.

2. **Check if `_posts/` exists.** If not, create the directory.

3. **Detect the site's theme and available layouts** by reading `_config.yml` (look for `theme`, `remote_theme`, or `layout` defaults). Use `post` as the default layout unless the config specifies otherwise.

4. **Generate the file** with this frontmatter template (populate all fields):

```yaml
---
layout: post
title: "Your Post Title Here"
date: YYYY-MM-DD HH:MM:SS +0000
last_modified_at: YYYY-MM-DD
author: # pull from _config.yml author field if present
categories: [Category]
tags: [tag1, tag2]
excerpt: "One or two sentences summarising the post — used in list views and meta descriptions."
image:                    # optional: /assets/img/post-cover.jpg
toc: false                # set true if post is long
comments: false           # set true if comments plugin is configured
---
```

5. **Append a placeholder body** below the frontmatter:

```markdown
## Introduction

Write your introduction here.

## Section One

Content goes here.

## Conclusion

Wrap up here.
```

6. **Report back** with the full file path created and remind the user to:
   - Fill in `categories` and `tags` relevant to the post topic.
   - Add a real `excerpt` (keeps SEO and list views tidy).
   - Set `image` if the theme supports a hero/cover image.

## Edge cases

- If a file for the same date + slug already exists, warn the user and ask whether to overwrite or use a different slug.
- If `_config.yml` defines `permalink` patterns (e.g. `/:year/:month/:title/`), mention it so the user knows the live URL.
- If the user supplies a future date, create the file with that date and note that Jekyll won't publish it until the build date passes (unless `future: true` is set in `_config.yml`).

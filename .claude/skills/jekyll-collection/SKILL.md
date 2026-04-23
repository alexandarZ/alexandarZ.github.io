# Jekyll Collection Skill

Set up a new Jekyll collection (projects, talks, recipes, etc.) — updates `_config.yml`, creates the collection folder, and generates an index page with Liquid loops.

## Steps

1. **Get collection details from the user** (or infer from context):
   - Collection name (singular noun, e.g. `project`, `talk`, `recipe`).
   - Whether documents should be output as individual pages (`output: true`).
   - Optional custom permalink pattern.

2. **Register the collection in `_config.yml`**:

```yaml
collections:
  projects:
    output: true
    permalink: /projects/:name/
```

   If a `collections` key already exists, append to it rather than replacing it.

3. **Add default frontmatter** (optional but recommended) so collection documents don't need to repeat `layout`:

```yaml
defaults:
  - scope:
      path: ""
      type: projects
    values:
      layout: project
```

4. **Create the collection folder** `_projects/` (Jekyll convention: underscore-prefixed folder matching the collection name).

5. **Add a sample document** `_projects/sample-project.md`:

```markdown
---
title: "Sample Project"
date: YYYY-MM-DD
tags: [web, open-source]
description: "One-line description used in list views."
link: https://github.com/user/repo
image: /assets/img/projects/sample.png
---

Full project description goes here.
```

6. **Create the collection layout** `_layouts/project.html` if it doesn't exist:

```html
---
layout: default
---
<article class="project">
  <h1>{{ page.title }}</h1>
  {% if page.image %}<img src="{{ page.image }}" alt="{{ page.title }}">{% endif %}
  <p class="project__meta">{{ page.date | date: "%B %d, %Y" }}</p>
  <div class="project__body">{{ content }}</div>
  {% if page.link %}
    <a href="{{ page.link }}" class="btn" target="_blank" rel="noopener">View Project</a>
  {% endif %}
</article>
```

7. **Create an index page** `projects.md` (or `projects/index.html`) with a Liquid loop:

```markdown
---
layout: page
title: Projects
permalink: /projects/
---

{% assign projects = site.projects | sort: "date" | reverse %}
{% for project in projects %}
  <div class="card">
    <h2><a href="{{ project.url }}">{{ project.title }}</a></h2>
    <p>{{ project.description }}</p>
  </div>
{% endfor %}
```

8. **Report back** with all files created and modified, and remind the user to restart `jekyll serve` so the new collection is loaded.

## Edge cases

- If `output: false`, documents are still accessible via `site.<collection>` in Liquid but won't generate individual pages — the index loop still works.
- If the collection name conflicts with a built-in Jekyll variable (`posts`, `pages`, `data`), warn the user and suggest an alternative name.
- For paginated collections, suggest `jekyll-paginate-v2` since the built-in `jekyll-paginate` only works with posts.
